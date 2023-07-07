# xscreensaver Video Fix

This script provides a fix for preventing the xscreensaver application from running while a video is playing. It addresses an issue where the screensaver activates even though a video is playing. So this script has a fix that stops this from checking if any audio is playing. It also has a section where it detects the use of spotify and does not stop the screensaver as it is not necessary.

## Usage

**This script has been checked in Ubuntu 22.04.**

1. Make sure you have the necessary dependencies installed:
   - `pacmd` - PulseAudio command-line interface
   - `dbus-send` - D-Bus message sender utility

2. Clone the repository:
   ```bash
   git clone https://github.com/Adithya2626/xscreensaver-Video-Fix
   ```

3. Go into the cloned directory:
   ```bash
   cd xscreensaver-Video-Fix
   ```

4. Make the script executable:
   ```bash
   chmod +x xscreensaver-Video-Fix.sh
   ```

5. Run the script:
   ```bash
   ./xscreensaver-Video-Fix.sh
   ```
   or **you can also make this script to run on startup.**

   
## Description

The script continuously monitors the state of audio playback and controls the behavior of the xscreensaver application accordingly. It prevents the screensaver from activating when audio is playing and allows the screensaver to activate when no audio is playing.

### Screensaver Video Fix Logic

The script follows the following logic:

1. Deletes the `Screensaver-status.csv` file if it already exists.
2. Creates the `Screensaver-status.csv` file with the necessary headers.
3. Enters an infinite loop to continuously monitor the state of audio playback and control the screensaver behavior.

When audio is playing:

- The script checks if the Spotify process is running using `pgrep -x "spotify" >/dev/null`.
- If Spotify is running and audio is playing, the screensaver is prevented from activating.
- The xscreensaver is paused using the `xscreensaver-command -exit` command.
- The script waits for 4 minutes to allow for continuous audio playback.
- After 4 minutes, the script checks the audio and Spotify status again.
- If the audio is still running and the Spotify status is not "Playing", indicating that audio playback is ongoing but Spotify playback is paused or stopped, the screensaver remains paused.
- If Spotify is closed, indicating that audio playback is ongoing but there is no active Spotify instance, the script resumes the screensaver using the `xscreensaver -nosplash` command and waits for 2 minutes before breaking the loop.
- If Spotify is running and the audio is playing, indicating that both audio and Spotify playback are active, the script resumes the screensaver using the `xscreensaver -nosplash` command and waits for 1 minute before breaking the loop.

When no audio is playing:

- The script allows the screensaver to activate when no audio playback is detected.
- It checks the state of the audio sink using the `pacmd list-sinks` command.
- If the audio sink state is "SUSPENDED" or "IDLE", indicating that no audio is playing, the screensaver is not paused and the script waits for 1 minute before checking the audio status again.
- If the audio sink state indicates that audio is running, the screensaver is paused using the `xscreensaver-command -exit` command.
- The script waits for 4 minutes to allow for continuous absence of audio playback.
- After 4 minutes, the script checks the audio status and Spotify status again.
- If the audio sink state is still running and the Spotify status is not "Playing", indicating that audio is running but Spotify playback is paused or stopped, the screensaver remains paused.
- If Spotify is closed, indicating that audio is running but there is no active Spotify instance, the script resumes the screensaver using the `xscreensaver -nosplash` command and waits for 1 minute before breaking the loop.
- If Spotify is running and the audio is playing, indicating that both audio and Spotify playback are active, the script resumes the screensaver using the `xscreensaver -nosplash` command and waits for 1 minute before breaking the loop.

The script continues to repeat this logic indefinitely, ensuring that the screensaver behavior is controlled based on the state of audio playback.

### Output

The script generates an `Screensaver-status.csv` file that logs the following information:

- Date: The date in the format "dd-mm-yyyy".
- Timestamp: The timestamp in the format "HH:MM".
- State: The state of the audio sink ("RUNNING", "SUSPENDED", "IDLE").
- Spotify Status: The status of Spotify playback ("Playing", "Paused", "Stopped").
- Output: Descriptive information about the action taken for screensaver control.

The `Screensaver-status.csv` file is continuously updated as the script runs.

## Contributing

Contributions are welcome! If you find any issues or have suggestions for improvements, please feel free to open an issue or create a pull request.

## License

This project is licensed under the [MIT License.](LICENSE)
