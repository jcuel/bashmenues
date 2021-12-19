Installation
On Debian based Linux distributions whiptail comes preinstalled.

If not you can grab it via:

apt-get install whiptail
Installation instructions for other Linux distros can be found at https://command-not-found.com/whiptail

Size of the dialog
All invocations of whiptail require you to specify a height (rows) and width (columns) of the dialog box to show. For example 20 100. It will take some trial and error before the box shows up nicely for your specific content.

Types of dialog boxes
Whiptail offers a couple of different types of dialog boxes.

Message box
A message box can be used to display some text. Script execution is paused until the user hits ENTER.

whiptail --msgbox "This is a message in a message box." 10 100
Whiptail message box

Yes/no box
Here you can ask the user a question, by default the Yes button is selected

#!/bin/bash

if whiptail --yesno "Are you sure?" 10 100; then
  echo "Yes I am sure!"
else
  echo "No I am not sure!"
fi
Whiptail yes/no box

Use the option --defaultno to make No the default selected button

#!/bin/bash

if whiptail --yesno "Are you sure?" 10 100 --defaultno; then
  echo "Yes I am sure!"
else
  echo "No I am not sure!"
fi
Whiptail yes/no box

Info box
An info box is basically a message box without Whiptail waiting for the user to hit ENTER.

This dialog box does not work on xterm terminals (for example the default terminal in Ubuntu)!

A workaround is possible though.

TERM=vt220 whiptail --infobox "Text from an info box." 10 100
Whiptail info box

Input box
Use an input box to ask the user to answer a question.

#!/bin/bash

NAME=$(whiptail --inputbox "Please enter your name" 10 100 3>&1 1>&2 2>&3)
echo "name: $NAME"
The 3>&1 1>&2 2>&3 part switches the stdout and stderr file descriptors. This is needed because we want to assign the user input to the variable NAME. This variable will be assigned whatever the subshell command outputs to stdout, only problem is that whiptail prints the input string to stderr by default. Credits to this Stack Overflow post.

Whiptail input box

Password box
A password is just like an input box however it displays an * for every inputted character.

PASSWORD=$(whiptail --passwordbox "Please enter your password" 10 100 3>&1 1>&2 2>&3)
Whiptail password box

Text box
A text box can be used to display the contents of a file. The --scrolltext option makes sure the user can scroll the dialog box.

whiptail --textbox file.txt 10 100 --scrolltext
Whiptail text box

Menu box
A menu can be used to present multiple options and having the user choose one option. Every menu item consists of a tag string and an item string. The tag string is the name of the menu item - and will be printed to stderr (we use the stderr/stdout flip trick again to print the selected option to stdout instead) when the user hits ENTER. The item string is a description of the menu item. Whiptail does not enforce tag strings to be unique.

After the height and width of the dialog box, another parameter is required: the height of the menu list. You probably want this value to be lower than the height of the dialog box.

#!/bin/bash

CHOICE=$(whiptail --menu "Choose an option" 18 100 10 \
  "Tiny" "A description for the tiny option." \
  "Small" "A description for the small option." \
  "Medium" "A description for the medium option." \
  "Large" "A description for the large option." \
  "Huge" "A description for the huge option." 3>&1 1>&2 2>&3)

if [ -z "$CHOICE" ]; then
  echo "No option was chosen (user hit Cancel)"
else
  echo "The user chose $CHOICE"
fi
Whiptail menu box

Checklist box
A checklist box is similar to a menu box, but it allows the user to select zero or more options.

#!/bin/bash

CHOICES=$(whiptail --separate-output --checklist "Choose options" 10 35 5 \
  "1" "The first option" ON \
  "2" "The second option" ON \
  "3" "The third option" OFF \
  "4" "The fourth option" OFF 3>&1 1>&2 2>&3)

if [ -z "$CHOICE" ]; then
  echo "No option was selected (user hit Cancel or unselected all options)"
else
  for CHOICE in $CHOICES; do
    case "$CHOICE" in
    "1")
      echo "Option 1 was selected"
      ;;
    "2")
      echo "Option 2 was selected"
      ;;
    "3")
      echo "Option 3 was selected"
      ;;
    "4")
      echo "Option 4 was selected"
      ;;
    *)
      echo "Unsupported item $CHOICE!" >&2
      exit 1
      ;;
    esac
  done
fi
Whiptail checklist box

Radiolist box
A radiolist is similar to a menu box but it allows you to set a default selected option.

#!/bin/bash

CHOICE=$(whiptail --separate-output --radiolist "Choose options" 10 35 5 \
  "1" "The first option" OFF \
  "2" "The second option" ON \
  "3" "The third option" OFF \
  "4" "The fourth option" OFF 3>&1 1>&2 2>&3)

if [ -z "$CHOICE" ]; then
  echo "No option was chosen (user hit Cancel)"
else
  echo "The user chose $CHOICE"
fi
Whiptail radiolist box

Gauge box
The last type, the gauge box, can be used to display a progress bar. Whiptail reads new percentages to update the progress bar from stdin.

The most difficult part of using this type of dialog box is knowing the progress of a command. This really depends on the command that is being run, and it makes this the most difficult dialog type to use in my opinion. You may find pv useful here.

Options
Some options you may find useful:

--fb
Display Full Buttons with a 3D-effect.

Whiptail message box with full buttons

--title
Add a title to the dialog box.

Whiptail message box with a title

--nocancel
Do not show a Cancel button. Can for example be used together with a menu box.

Tips
Full screen?
In case you decide you want a full screen dialog box, you can use stty to give you the dimensions (number of rows and columns) of the terminal.

whiptail --msgbox "Full screen!" $(stty size)
