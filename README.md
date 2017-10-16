# derpslack
Theming Slack for PC/MacOsX


To theme slack, we will be appending some code into index.js to call for a CSS to style slack.

So, first thing we need to do is locate your Slack application directory

- **Windows:** %homepath%\AppData\Local\slack\
- **Mac:** /Applications/Slack.app/Contents/

and, in it, we're going to open and edit **index.js**

We will be appending this code to the bottom of the file:

```
// First make sure the wrapper app is loaded
document.addEventListener("DOMContentLoaded", function() {

   // Then get its webviews
   let webviews = document.querySelectorAll(".TeamView webview");

   // Fetch our CSS in parallel ahead of time
   const cssPath = 'https://gist.github.com/seiretsym/bc7590617eb4fdc42c026e23b2060199/raw/c155880e8eb7f38c2ba86b30bd048a3387814861/derpslack.css';
   let cssPromise = fetch(cssPath).then(response => response.text());

   let customCustomCSS = `
   :root {
      /* Modify these to change your theme colors: */
      --primary: #CCC;
      --text: #CCC;
      --background: #4C2A17;
      --background-elevated: #2B1306;
      --border-dim: #50964D;
      --border-bright: #50964D;
   }
   `

   // Insert a style tag into the wrapper view
   cssPromise.then(css => {
      let s = document.createElement('style');
      s.type = 'text/css';
      s.innerHTML = css + customCustomCSS;
      document.head.appendChild(s);
   });

   // Wait for each webview to load
   webviews.forEach(webview => {
      webview.addEventListener('ipc-message', message => {
         if (message.channel == 'didFinishLoading')
            // Finally add the CSS into the webview
            cssPromise.then(css => {
               let script = `
                     let s = document.createElement('style');
                     s.type = 'text/css';
                     s.id = 'slack-custom-css';
                     s.innerHTML = \`${css + customCustomCSS}\`;
                     document.head.appendChild(s);
                     `
               webview.executeJavaScript(script);
            })
      });
   });
});
```
*Credit to widget- https://github.com/widget-/slack-black-theme*

Save and reload Slack.
