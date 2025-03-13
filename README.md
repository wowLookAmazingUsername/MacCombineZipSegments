# Combine Zip Segments
An Automator Application that uncompresses segmented zip files (eg. from google drive) into a single directory

This automation uses Automator to ask for two folders: the first folder is where you put all of the segmented zip files, and the second is where you want to decompress.

It is identical to running the following commands in your terminal:

  cd /your/source/folder
  unzip '*.zip' -d /your/destination/folder

You can find the automation's "source code" below.


<img width="867" alt="Screenshot 2025-03-13 at 13 51 04" src="https://github.com/user-attachments/assets/7db415e3-e4b9-4704-9028-23c5a5e96078" />

AppleScript action code:

  on run {input, parameters}
      -- Ensure there are at least two items provided
      if (count of input) < 2 then
          display dialog "Please provide both a source folder and a destination folder." buttons {"OK"} default button 1
          return
      end if
  
      -- Get the source and destination folders
      set sourceFolder to item 1 of input
      set destinationFolder to item 2 of input
  
      -- Convert to POSIX paths
      set sourcePosix to POSIX path of sourceFolder
      set destinationPosix to POSIX path of destinationFolder
  
      -- Build the shell command to loop over each zip file and unzip it individually.
      set shellCommand to "cd " & quoted form of sourcePosix & " && " & ¬
          "for file in *.zip; do " & ¬
          "echo \"Extracting: $file\"; " & ¬
          "unzip \"$file\" -d " & quoted form of destinationPosix & "; " & ¬
          "done; " & ¬
          "echo 'Extraction complete. Press Return to close this window.'; read"
  
      -- Tell Terminal to open and run the command
      tell application "Terminal"
          activate
          do script shellCommand
      end tell
  
      return input
  end run
