#!/bin/bash

output_file="Screensaver-status.csv"  #Enter a file path of your choice

# Delete the output file if it already exists
if [ -f "$output_file" ]; then
  rm "$output_file"
fi

# Create the output file with headers and filter options
echo -e "Date;Timestamp;State;Spotify Status;Output" >"$output_file"
echo ";;;" >>"$output_file"

while true; do
  if pgrep -x "spotify" >/dev/null; then
    while true; do
      # Get the state of the default audio sink
      state=$(pacmd list-sinks | grep -A 4 "*" | grep "state: " | cut -c 9-)

      # Get the playback status of Spotify using D-Bus
      spotify_status=$(dbus-send --print-reply --dest=org.mpris.MediaPlayer2.spotify /org/mpris/MediaPlayer2 org.freedesktop.DBus.Properties.Get string:"org.mpris.MediaPlayer2.Player" string:"PlaybackStatus" | awk '/string/ {gsub(/"/, "", $3); print $3}')

      if [[ $state == "RUNNING" && $spotify_status != "Playing" ]]; then
        # If audio is running but Spotify is not playing
        echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;$spotify_status;Pausing screensaver" >> "$output_file"
        xscreensaver-command -exit >/dev/null
        sleep 4m

        while true; do
          # Check audio and Spotify status again
          state=$(pacmd list-sinks | grep -A 4 "*" | grep "state: " | cut -c 9-)
          spotify_status=$(dbus-send --print-reply --dest=org.mpris.MediaPlayer2.spotify /org/mpris/MediaPlayer2 org.freedesktop.DBus.Properties.Get string:"org.mpris.MediaPlayer2.Player" string:"PlaybackStatus" | awk '/string/ {gsub(/"/, "", $3); print $3}')

          if [[ $state == "RUNNING" && $spotify_status != "Playing" ]]; then
            # If audio is still running but Spotify is not playing
            if pgrep -x "spotify" >/dev/null; then
              # If Spotify is running
              echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;$spotify_status;Keeping the screensaver paused" >> "$output_file"
              sleep 2m
            else
              # If Spotify is closed
              echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;$spotify_status;Resuming screensaver (Spotify closed)" >>"$output_file"
              xscreensaver -nosplash >/dev/null &
              sleep 2m
              break 2
            fi
          else
            # If audio is running and Spotify is playing
            if pgrep -x "spotify" >/dev/null; then
              echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;$spotify_status;Resuming screensaver">>"$output_file"
              xscreensaver -nosplash >/dev/null &
              sleep 1m
              break
            else
              echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;$spotify_status;Resuming screensaver (Spotify closed)" >>"$output_file"
              xscreensaver -nosplash >/dev/null &
              sleep 2m
              break 2
            fi
          fi
        done
      else
        # If audio is running and Spotify is playing
        echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;$spotify_status;Not pausing screensaver">>"$output_file"
        sleep 1m
        break
      fi
    done
  else
    while true; do
      # Check the state of the audio sink
      state=$(pacmd list-sinks | grep -A 4 "*" | grep "state: " | cut -c 9-)
      
      if [[ $state == "SUSPENDED" || $state == "IDLE" ]]; then
        # If audio is suspended or idle
        echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;Not running;Not pausing screensaver">>"$output_file"
        sleep 1m
        break
      else
        # If audio is running
        echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;Not running;Pausing screensaver">>"$output_file"
        xscreensaver-command -exit >/dev/null
        sleep 4m

        while true; do
          # Check the state of the audio sink again
          state=$(pacmd list-sinks | grep -A 4 "*" | grep "state: " | cut -c 9-)

          if [[ $state == "SUSPENDED" || $state == "IDLE" ]]; then
            # If audio is suspended or idle
            if pgrep -x "spotify" >/dev/null; then
              # If Spotify is running
              echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;Opened;Resuming screensaver" >>"$output_file"
              xscreensaver -nosplash >/dev/null &
              sleep 1m
              break 2
            else
              # If Spotify is closed
              echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;Not running;Resuming screensaver">>"$output_file"
              xscreensaver -nosplash >/dev/null &
              sleep 1m
              break
            fi
          else
            # If audio is running
            if ! pgrep -x "spotify" >/dev/null; then
              # If Spotify is not running
              echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;Not running;Keeping the screensaver paused" >>"$output_file"
              sleep 2m
            else
              # If Spotify is running
              echo -e "$(date '+%d-%m-%Y');$(date '+%H:%M');$state;Opened;Resuming screensaver" >>"$output_file"
              xscreensaver -nosplash >/dev/null &
              sleep 1m
              break 2
            fi
          fi
        done
      fi
    done
  fi
done
