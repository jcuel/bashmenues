# Installation

On Debian based Linux distributions whiptail comes preinstalled.

If not you can grab it via:

| 1
 | apt-get install whiptail
 |
| --- | --- |

Installation instructions for other Linux distros can be found at [https://command-not-found.com/whiptail](https://command-not-found.com/whiptail)

# Size of the dialog

All invocations of whiptail require you to specify a height (rows) and width (columns) of the dialog box to show. For example 20 100. It will take some trial and error before the box shows up nicely for your specific content.

# Types of dialog boxes

Whiptail offers a couple of different types of dialog boxes.

Message box

A message box can be used to display some text. Script execution is paused until the user hits ENTER.

| 1
 | whiptail --msgbox&quot;This is a message in a message box.&quot; 10 100
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_34b0eedc518cae70.png)](https://gijs-de-jong.nl/assets/img/whiptail/messagebox.png)

Yes/no box

Here you can ask the user a question, by default the Yes button is selected

| 1234567
 | _#!/bin/bash_
**if** whiptail --yesno&quot;Are you sure?&quot; 10 100; **then** echo&quot;Yes I am sure!&quot; **else** echo&quot;No I am not sure!&quot; **fi**
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_ee535097b3d42150.png)](https://gijs-de-jong.nl/assets/img/whiptail/yesno-default-yes.png)

Use the option --defaultno to make No the default selected button

| 1234567
 | _#!/bin/bash_
**if** whiptail --yesno&quot;Are you sure?&quot; 10 100 --defaultno; **then** echo&quot;Yes I am sure!&quot; **else** echo&quot;No I am not sure!&quot; **fi**
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_f0252ff7aff6722f.png)](https://gijs-de-jong.nl/assets/img/whiptail/yesno-default-no.png)

Info box

An info box is basically a message box without Whiptail waiting for the user to hit ENTER.

This dialog box  **does not work**  on xterm terminals (for example the default terminal in Ubuntu)!

A [workaround](https://stackoverflow.com/questions/13000391/how-to-display-infobox-in-whiptail) is possible though.

| 1
 | TERM **=** vt220 whiptail --infobox&quot;Text from an info box.&quot; 10 100
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_d0b55200e1121ae6.png)](https://gijs-de-jong.nl/assets/img/whiptail/infobox.png)

Input box

Use an input box to ask the user to answer a question.

| 1234
 | _#!/bin/bash_
NAME **=** $(whiptail --inputbox&quot;Please enter your name&quot; 10 100 3\&gt;&amp;1 1\&gt;&amp;2 2\&gt;&amp;3)echo&quot;name: $NAME&quot;
 |
| --- | --- |

The 3\&gt;&amp;1 1\&gt;&amp;2 2\&gt;&amp;3 part switches the stdout and stderr file descriptors. This is needed because we want to assign the user input to the variable NAME. This variable will be assigned whatever the subshell command outputs to stdout, only problem is that whiptail prints the input string to stderr by default. Credits to [this Stack Overflow post](https://unix.stackexchange.com/questions/42728/what-does-31-12-23-do-in-a-script).

[![](RackMultipart20211219-4-1010wp0_html_7e17d48ed42fedf3.png)](https://gijs-de-jong.nl/assets/img/whiptail/inputbox.png)

Password box

A password is just like an input box however it displays an \* for every inputted character.

| 1
 | PASSWORD **=** $(whiptail --passwordbox&quot;Please enter your password&quot; 10 100 3\&gt;&amp;1 1\&gt;&amp;2 2\&gt;&amp;3)
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_36428daa1359a85b.png)](https://gijs-de-jong.nl/assets/img/whiptail/password-box.png)

Text box

A text box can be used to display the contents of a file. The --scrolltext option makes sure the user can scroll the dialog box.

| 1
 | whiptail --textbox file.txt 10 100 --scrolltext
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_4c360ea319d9e58c.png)](https://gijs-de-jong.nl/assets/img/whiptail/textbox.png)

Menu box

A menu can be used to present multiple options and having the user choose one option. Every menu item consists of a tag string and an item string. The tag string is the name of the menu item - and will be printed to stderr (we use the stderr/stdout flip trick again to print the selected option to stdout instead) when the user hits ENTER. The item string is a description of the menu item. Whiptail does not enforce tag strings to be unique.

After the height and width of the dialog box, another parameter is required: the height of the menu list. You probably want this value to be lower than the height of the dialog box.

| 1234567891011121314
 | _#!/bin/bash_
CHOICE **=** $(whiptail --menu&quot;Choose an option&quot; 18 100 10 \&quot;Tiny&quot;&quot;A description for the tiny option.&quot;\&quot;Small&quot;&quot;A description for the small option.&quot;\&quot;Medium&quot;&quot;A description for the medium option.&quot;\&quot;Large&quot;&quot;A description for the large option.&quot;\&quot;Huge&quot;&quot;A description for the huge option.&quot; 3\&gt;&amp;1 1\&gt;&amp;2 2\&gt;&amp;3)
**if**** [**-z&quot;$CHOICE&quot;**] **;** then**echo&quot;No option was chosen (user hit Cancel)&quot;**else **echo&quot;The user chose $CHOICE&quot;** fi**
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_ce7003a8db08ff2e.png)](https://gijs-de-jong.nl/assets/img/whiptail/menu-box.png)

Checklist box

A checklist box is similar to a menu box, but it allows the user to select zero or more options.

| 1234567891011121314151617181920212223242526272829303132
 | _#!/bin/bash_
CHOICES **=** $(whiptail --separate-output--checklist&quot;Choose options&quot; 10 35 5 \&quot;1&quot;&quot;The first option&quot; ON \&quot;2&quot;&quot;The second option&quot; ON \&quot;3&quot;&quot;The third option&quot; OFF \&quot;4&quot;&quot;The fourth option&quot; OFF 3\&gt;&amp;1 1\&gt;&amp;2 2\&gt;&amp;3)
**if**** [**-z&quot;$CHOICE&quot;**] **;** then**echo&quot;No option was selected (user hit Cancel or unselected all options)&quot;**else ****for** CHOICE **in** $CHOICES; **do**** case **&quot;$CHOICE&quot;** in**&quot;1&quot;)echo&quot;Option 1 was selected&quot;;;&quot;2&quot;)echo&quot;Option 2 was selected&quot;;;&quot;3&quot;)echo&quot;Option 3 was selected&quot;;;&quot;4&quot;)echo&quot;Option 4 was selected&quot;;;**\***)echo&quot;Unsupported item $CHOICE!&quot;**\&gt; **&amp;2exit 1;;** esac ****done**** fi**
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_f60bd1015be63edb.png)](https://gijs-de-jong.nl/assets/img/whiptail/checklist-box.png)

Radiolist box

A radiolist is similar to a [menu box](https://gijs-de-jong.nl/posts/pretty-dialog-boxes-for-your-shell-scripts-using-whiptail/#menu-box) but it allows you to set a default selected option.

| 12345678910111213
 | _#!/bin/bash_
CHOICE **=** $(whiptail --separate-output--radiolist&quot;Choose options&quot; 10 35 5 \&quot;1&quot;&quot;The first option&quot; OFF \&quot;2&quot;&quot;The second option&quot; ON \&quot;3&quot;&quot;The third option&quot; OFF \&quot;4&quot;&quot;The fourth option&quot; OFF 3\&gt;&amp;1 1\&gt;&amp;2 2\&gt;&amp;3)
**if**** [**-z&quot;$CHOICE&quot;**] **;** then**echo&quot;No option was chosen (user hit Cancel)&quot;**else **echo&quot;The user chose $CHOICE&quot;** fi**
 |
| --- | --- |

[![](RackMultipart20211219-4-1010wp0_html_7b425ef3d0a1e76a.png)](https://gijs-de-jong.nl/assets/img/whiptail/radiolist.png)

Gauge box

The last type, the gauge box, can be used to display a progress bar. Whiptail reads new percentages to update the progress bar from stdin.

The most difficult part of using this type of dialog box is knowing the progress of a command. This really depends on the command that is being run, and it makes this the most difficult dialog type to use in my opinion. You may find [pv](https://linux.die.net/man/1/pv) useful here.

# Options

Some options you may find useful:

--fb

Display Full Buttons with a 3D-effect.

[![](RackMultipart20211219-4-1010wp0_html_3bb85b311186de8e.png)](https://gijs-de-jong.nl/assets/img/whiptail/messagebox-fb.png)

--title

Add a title to the dialog box.

[![](RackMultipart20211219-4-1010wp0_html_6ace643ac6c572c6.png)](https://gijs-de-jong.nl/assets/img/whiptail/messagebox-title.png)

--nocancel

Do not show a Cancel button. Can for example be used together with a menu box.

# Tips

Full screen?

In case you decide you want a full screen dialog box, you can use stty to give you the dimensions (number of rows and columns) of the terminal.

| 1
 | whiptail --msgbox&quot;Full screen!&quot;$(stty size)
 |
| --- | --- |
