
# Using Debugger
Add a debugger statement inside the code that you want to debug.
Run the node inspect index.js or node inspect server.js command to start the application in debug mode.
Access the URL chrome://inspect in your Chrome browser.
Click on the inspect link under the Remote Target section.
Click on the blue triangle icon to skip debugging if you don't want to start debugging your application from the first line of the index.js or server.js file.
Make an API call or do something that will trigger the code where you added the debugger; statement. This way you can debug the code line by line and find out the issue.