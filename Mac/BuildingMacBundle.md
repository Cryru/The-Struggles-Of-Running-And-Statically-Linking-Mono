# How do I build a Mac .app Bundle?

First of all verify that your application actually runs on Mac using the instructions in the main ReadMe.

All done? Great, now we can start.

Extract the zip file found in the same folder as this file and put your application inside it at the `Contents/MacOS` path. Next you can create an icon for it following the instructions within the text file in `Contents/Resources`. Next edit the `Info.plist` file found in the `Contents` folder. There you will need to input the name of your icon file and executable, note that I didn't say path but name, also don't input any file extension.

# Notes

- Although you specifically point to your executable file in the `Info.plist` I've had the best of luck when the executable file has the same name as the `.app` folder. If using the run script from this repo make sure to name it properly.

- Sometimes your application will fail to open even when you've done everything correctly. In this case you might want to try the platform specific fix listed in the main ReadMe of adding execution permissions to the script and built button.

ex.

`chmod +x Example.app/Contents/MacOS/Example`

`chmod +x Example.app/Contents/MacOS/AppMac`