import os
import time
import subprocess

# Configuration
WATCH_DIR = os.path.expanduser("~/PrintDrop")
PRINTER_NAME = "MX05"  # Swap with your actual printer name or MAC address

print(f"Monitoring folder: {WATCH_DIR} for new files...")

while True:
    try:
        # Scan the folder for files
        files = [f for f in os.listdir(WATCH_DIR) if os.path.isfile(os.path.join(WATCH_DIR, f))]

        for file_name in files:
            file_path = os.path.join(WATCH_DIR, file_name)
            print(f"Found file: {file_name}. Processing...")

            # Trigger the real print.py script to handle the BLE thermal conversion
            # (Adjust 'print.py' path or flags to match your exact repository version)
            result = subprocess.run(["python3", "print.py", "--name", PRINTER_NAME, file_path])

            # Delete the file only if the print script executed successfully
            if result.returncode == 0:
                os.remove(file_path)
                print(f"Successfully printed and deleted: {file_name}")
            else:
                print(f"Printing failed for {file_name}. Leaving file in folder.")

    except Exception as e:
        print(f"Error in watcher loop: {e}")

    # Pause for 2 seconds before scanning the directory again to save CPU cycles
    time.sleep(2)
