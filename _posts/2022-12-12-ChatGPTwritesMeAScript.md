---
layout: post
title:  ChatGPT writes me a script
categories: [Machine Learning, GPT-3, ChatGPT, Robot Overlords]
excerpt: At the request of Rob Beynon, I am posting the conversation I with ChatGPT to have it write me a script. 
---

I am going to keep this light, but ChatGPT (a GPT-3.5 implementation of InstructGPT by OpenAI, currently in some sort of public beta) can seemingly do anything if you give it the right background, use the right words, and sometimes walk it through some logic or background. It excels at coding [(reading, writing, translating, commenting, debugging, etc.)](https://twitter.com/svpino/status/1611357154514186241?s=43&t=RBklJMT9rU6-ZiRvkIhkbQ) or even [hallucinating operating systems](https://arstechnica.com/information-technology/2022/12/openais-new-chatbot-can-hallucinate-a-linux-shell-or-calling-a-bbs/).

Though I have a more critical task I need its help with (maybe a later post), last Friday I needed it to write me a powershell script for a very specific use-case. If you have been in the last few ABRF studies I am helping run, I pre-populate an FTP directory with participant folders (just some two digit number I assign) and I put a set of template files in each folder with the number as a prefix. I have done this by hand, which isn't that hard, but I figured ChatGPT could help make a powershell to do this automagically. (note, I thought I could still do .sh but windows 10 needs more to run this, so actually this was my second conversation so I could have powershell, first was bash) The only thing needed by me is to set the total number of participants, and the script will copy the files in the directory I place it and go to town. It may not be perfect (be sure to scroll down to see how I came back days later to ask it to ignore the ps1 file when copying things over), but you can see how the process worked. Also, I am a very out of practice coder who is a power user fanboy at best.


**Me**: can you write me a powershell script?

**ChatGPT**: Sure, here is a simple powershell script that prints "Hello, World!" to the console:

```
Write-Output "Hello, World!"
```

To run this script, save it to a .ps1 file and use the powershell command to execute it, like this:

```
powershell .\hello-world.ps1
```

This will run the script and print "Hello, World!" to the console. Let me know if you have any questions.

**Me**: Can you write a powershell script to make a copy of all the files in a folder and change the new file names by prefixing a two digit number?

**ChatGPT**: Sure, here is a simple powershell script that will make a copy of all the files in a folder and prefix the new file names with a two-digit number:

```
$folder = "C:\path\to\folder"
$files = Get-ChildItem -Path $folder

# Create a counter to use as the prefix for the new file names
$counter = 1

# Loop through each file in the folder
foreach ($file in $files) {
    # Generate the new file name by prefixing the counter with a two-digit number
    $newFileName = "{0:D2}-{1}" -f $counter, $file.Name

    # Make a copy of the file with the new name
    Copy-Item -Path $file.FullName -Destination "$folder\$newFileName"

    # Increment the counter for the next file
    $counter++
}
```

To use this script, save it to a .ps1 file and run it using the powershell command. Be sure to update the $folder variable with the path to the folder you want to copy the files from.

**Me**: Can you change this so all the copies have the same number prefixed?

**ChatGP**: Sure, here is an updated version of the script that will make a copy of all the files in a folder and prefix the new file names with the same two-digit number:

```
$folder = "C:\path\to\folder"
$files = Get-ChildItem -Path $folder

# Set the number to use as the prefix for the new file names
$number = "01"

foreach ($file in $files) {
    # Generate the new file name by prefixing the number
    $newFileName = "$number-$file.Name"

    # Make a copy of the file with the new name
    Copy-Item -Path $file.FullName -Destination "$folder\$newFileName"
}
```

To use this script, save it to a .ps1 file and run it using the powershell command. Be sure to update the $folder and $number variables with the path to the folder you want to copy the files from and the number you want to use as the prefix.

**Me**: I don't want the copies to end in .Name, can you change it?

**ChatGPT**: Sure, here is an updated version of the script that will make a copy of all the files in a folder and prefix the new file names with the same two-digit number, without including the .Name extension in the new file names:

```
$folder = "C:\path\to\folder"
$files = Get-ChildItem -Path $folder

# Set the number to use as the prefix for the new file names
$number = "01"

foreach ($file in $files) {
    # Generate the new file name by prefixing the number
    $newFileName = "$number-$file"

    # Make a copy of the file with the new name
    Copy-Item -Path $file.FullName -Destination "$folder\$newFileName"
}
```

To use this script, save it to a .ps1 file and run it using the powershell command. Be sure to update the $folder and $number variables with the path to the folder you want to copy the files from and the number you want to use as the prefix.

**Me**: That worked, thanks. Can you change it make multiple sets of copies, with each set getting a two digit prefix?

**ChatGPT**: Sure, here is an updated version of the script that will make multiple sets of copies of the files in a folder, with each set of copies being prefixed with a different two-digit number:

```
$folder = "C:\path\to\folder"
$files = Get-ChildItem -Path $folder

# Set the numbers to use as the prefix for each set of copies
$numbers = "01", "02", "03"

# Loop through each number
foreach ($number in $numbers) {
    # Loop through each file in the folder
    foreach ($file in $files) {
        # Generate the new file name by prefixing the number
        $newFileName = "$number-$file"

        # Make a copy of the file with the new name
        Copy-Item -Path $file.FullName -Destination "$folder\$newFileName"
    }
}
```

To use this script, save it to a .ps1 file and run it using the powershell command. Be sure to update the $folder and $numbers variables with the path to the folder you want to copy the files from and the numbers you want to use as the prefixes.

**Me**: Is there a way for me to specify the total number of sets, and they are created with an index starting at 1?

**ChatGPT**: Yes, here is an updated version of the script that will make a specified number of sets of copies of the files in a folder, with each set of copies being prefixed with a two-digit number starting at 01 and incrementing by one for each set of copies:

```
$folder = "C:\path\to\folder"
$files = Get-ChildItem -Path $folder

# Set the total number of sets of copies to create
$totalSets = 3

# Create a counter to use as the prefix for each set of copies
$counter = 1

# Loop through the total number of sets
for ($i = 0; $i -lt $totalSets; $i++) {
    # Generate the number to use as the prefix for this set of copies
    $number = "{0:D2}" -f $counter

    # Loop through each file in the folder
    foreach ($file in $files) {
        # Generate the new file name by prefixing the number
        $newFileName = "$number-$file"

        # Make a copy of the file with the new name
        Copy-Item -Path $file.FullName -Destination "$folder\$newFileName"
    }

    # Increment the counter for the next set of copies
    $counter++
}
```

To use this script, save it to a .ps1 file and run it using the powershell command. Be sure to update the $folder and $totalSets variables with the path to the folder you want to copy the files from and the total number of sets of copies you want to create.

**Me**: Perfect! Can you make it so the folder variable is based on the folder where the ps1 file is located?

**ChatGPT**: Sure, here is an updated version of the script that will make a specified number of sets of copies of the files in a folder, with each set of copies being prefixed with a two-digit number starting at 01 and incrementing by one for each set of copies. This version of the script uses the current directory where the script is run as the folder to copy the files from:

```
# Get the current directory where the script is run
$folder = Get-Location

# Get the files in the current directory
$files = Get-ChildItem -Path $folder

# Set the total number of sets of copies to create
$totalSets = 3

# Create a counter to use as the prefix for each set of copies
$counter = 1

# Loop through the total number of sets
for ($i = 0; $i -lt $totalSets; $i++) {
    # Generate the number to use as the prefix for this set of copies
    $number = "{0:D2}" -f $counter

    # Loop through each file in the folder
    foreach ($file in $files) {
        # Generate the new file name by prefixing the number
        $newFileName = "$number-$file"

        # Make a copy of the file with the new name
        Copy-Item -Path $file.FullName -Destination "$folder\$newFileName"
    }

    # Increment the counter for the next set of copies
    $counter++
}
```

To use this script, save it to a .ps1 file and run it using the powershell command. Be sure to update the $totalSets variable with the total number of sets of copies you want to create.

**Me**: That works even better, thanks! Last request, can you make a folder for each set of copies, and name each folder the respective two-digit number that the copied files used?

**ChatGPT**: Sure, here is an updated version of the script that will make a specified number of sets of copies of the files in a folder, with each set of copies being prefixed with a two-digit number starting at 01 and incrementing by one for each set of copies. This version of the script also creates a new folder for each set of copies, and names each folder with the two-digit number that is used as the prefix for that set of copies:

```
# Get the current directory where the script is run
$folder = Get-Location

# Get the files in the current directory
$files = Get-ChildItem -Path $folder

# Set the total number of sets of copies to create
$totalSets = 3

# Create a counter to use as the prefix for each set of copies
$counter = 1

# Loop through the total number of sets
for ($i = 0; $i -lt $totalSets; $i++) {
    # Generate the number to use as the prefix for this set of copies
    $number = "{0:D2}" -f $counter

    # Create a new folder for this set of copies
    New-Item -ItemType Directory -Path "$folder\$number"

    # Loop through each file in the folder
    foreach ($file in $files) {
        # Generate the new file name by prefixing the number
        $newFileName = "$number-$file"

        # Make a copy of the file with the new name and move it to the new folder
        Copy-Item -Path $file.FullName -Destination "$folder\$number\$newFileName"
    }

    # Increment the counter for the next set of copies
    $counter++
}
```

To use this script, save it to a .ps1 file and run it using the powershell command. Be sure to update the $totalSets variable with the total number of sets of copies you want to create.

**Me**: Wow, you just saved me a ton of time. Thank you so much!

**ChatGPT**: You're welcome! I'm glad I could help. Let me know if you have any other questions or if there is anything else I can do for you.

## Days Later


Now as I was writing this post I realized the mistake that it would copy the ps1 file as well. This made an opportunity to see how I can use ChatGPT when I can't save the console. The original conversation was Friday, and this below was Monday. I pasted it's last description of the code, and the code, and my request.


**Me**: Here is a powershell script that will make a specified number of sets of copies of the files in a folder, with each set of copies being prefixed with a two-digit number starting at 01 and incrementing by one for each set of copies. This version of the script also creates a new folder for each set of copies, and names each folder with the two-digit number that is used as the prefix for that set of copies. Can you modify it to ignore any ps1 files?

```
# Get the current directory where the script is run
$folder = Get-Location

# Get the files in the current directory
$files = Get-ChildItem -Path $folder

# Set the total number of sets of copies to create
$totalSets = 3

# Create a counter to use as the prefix for each set of copies
$counter = 1

# Loop through the total number of sets
for ($i = 0; $i -lt $totalSets; $i++) {
    # Generate the number to use as the prefix for this set of copies
    $number = "{0:D2}" -f $counter

    # Create a new folder for this set of copies
    New-Item -ItemType Directory -Path "$folder\$number"

    # Loop through each file in the folder
    foreach ($file in $files) {
        # Generate the new file name by prefixing the number
        $newFileName = "$number-$file"

        # Make a copy of the file with the new name and move it to the new folder
        Copy-Item -Path $file.FullName -Destination "$folder\$number\$newFileName"
    }

    # Increment the counter for the next set of copies
    $counter++
}
```

**ChatGP**: To ignore any .ps1 files in the script, you can add a simple if statement to check the file extension of each file and only copy files that do not have the .ps1 extension. Here is an example of how you could modify the script to do this:

```
# Get the current directory where the script is run
$folder = Get-Location

# Get the files in the current directory
$files = Get-ChildItem -Path $folder

# Set the total number of sets of copies to create
$totalSets = 3

# Create a counter to use as the prefix for each set of copies
$counter = 1

# Loop through the total number of sets
for ($i = 0; $i -lt $totalSets; $i++) {
    # Generate the number to use as the prefix for this set of copies
    $number = "{0:D2}" -f $counter

    # Create a new folder for this set of copies
    New-Item -ItemType Directory -Path "$folder\$number"

    # Loop through each file in the folder
    foreach ($file in $files) {
        # Check the file extension and only continue if it's not .ps1
        if ($file.Extension -ne ".ps1") {
            # Generate the new file name by prefixing the number
            $newFileName = "$number-$file"

            # Make a copy of the file with the new name and move it to the new folder
            Copy-Item -Path $file.FullName -Destination "$folder\$number\$newFileName"
        }
    }

    # Increment the counter for the next set of copies
    $counter++
}
```

In this modified version of the script, the if statement checks the Extension property of each $file object in the $files array, and only continues with the copy operation if the file extension is not .ps1. This will prevent any .ps1 files from being copied. Let me know if this helps or if you have any other questions.

**Me**: That did the trick. I didn't know how much code I could paste into the chat.

**ChatGPT**: You can paste as much code as you like into the chat! I'm here to help with any coding questions you have, so feel free to include as much code as you need to in order for me to understand your question and provide a helpful response. If you have any other questions, don't hesitate to ask. I'm happy to help.


&nbsp;  
&nbsp;  
&nbsp;  
