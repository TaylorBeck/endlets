# endlets
Bookmarklet to find endpoints easily with one click (<em>this is just a fork of [0dayCTF's project](https://github.com/0dayCTF/endlets) that adds a modal and async/await</em>)

![endlets-modal](https://github.com/TaylorBeck/endlets/assets/1897307/7fe42029-e7fe-428c-b92d-c1eaaf9eb510)

This is a powerful JavaScript bookmarklet designed to find and display all endpoint URLs within a webpage by recursively fetching and processing scripts. This tool is useful for developers, security researchers, and bug hunters.

## Installation
Open your browser's bookmarks manager.
Create a new bookmark.
Set the bookmark's name to "Show Endpoints".
## Copy the following JavaScript code and set it as the URL for the bookmark:
```javascript
javascript:(async function() { const urlRegex = /(?<=(\"|\%27|\`))\/[a-zA-Z0-9_?&=\/\-\#\.]*(?=(\"|\'|\%60))/g; const foundUrls = new Set(); const visitedUrls = new Set(); const maxDepth = 3; const modal = document.createElement("div"); const modalStyles = { position: "fixed", top: "50%", left: "50%", transform: "translate(-50%, -50%)", opacity: ".95", backgroundColor: "#fff", padding: "20px", borderRadius: "8px", maxWidth: "80%", maxHeight: "80%", overflowY: "auto", zIndex: "1000" }; Object.assign(modal.style, modalStyles); const closeButton = document.createElement("button"); closeButton.textContent = "X"; const closeButtonStyles = { position: "absolute", top: "10px", right: "10px", border: "none", background: "none", color: "#333", fontWeight: "bold", fontSize: "16px", cursor: "pointer" }; Object.assign(closeButton.style, closeButtonStyles); closeButton.onclick = () => { document.body.removeChild(modal); }; modal.appendChild(closeButton); const modalContent = document.createElement("div"); modalContent.style.textAlign = "left"; modal.appendChild(modalContent); document.body.appendChild(modal); async function fetchAndExtract(url, depth) { if (depth > maxDepth || visitedUrls.has(url)) return; visitedUrls.add(url); try { const response = await fetch(url); if (!response.ok) throw new Error(`Failed to fetch ${url}`); const content = await response.text(); const matches = content.matchAll(urlRegex); for (const match of matches) { let extractedUrl = match[0]; foundUrls.add(extractedUrl); if (extractedUrl.startsWith("/")) { extractedUrl = window.location.origin + extractedUrl; } const urlElement = document.createElement("div"); urlElement.textContent = extractedUrl.replace(window.location.origin, ""); urlElement.style.color = "#333"; modalContent.appendChild(urlElement); } } catch (error) { console.error("An error occurred while fetching script:", error); } } async function extractFromScripts() { const scriptTags = document.getElementsByTagName("script"); for (const script of scriptTags) { const src = script.src; if (src) { await fetchAndExtract(src, 0); } } } async function extractFromPage() { const pageContent = document.documentElement.outerHTML; const matches = pageContent.matchAll(urlRegex); for (const match of matches) { let extractedUrl = match[0]; foundUrls.add(extractedUrl); if (extractedUrl.startsWith("/")) { extractedUrl = window.location.origin + extractedUrl; } const urlElement = document.createElement("div"); urlElement.textContent = extractedUrl.replace(window.location.origin, ""); urlElement.style.color = "#333"; modalContent.appendChild(urlElement); } } await extractFromScripts(); await extractFromPage(); })();
```
## Usage
Navigate to the webpage you want to analyze.
Click on the "Show Endpoints" bookmark.
Wait a few seconds for the bookmarklet to fetch and process the scripts.
The extracted endpoints will be displayed in a modal.
This will also work on mobile browsers if you sync your bookmarks.
  
*warning -- If you're authenicated, this script will sometimes emulate behavior on your behalf.*  
## How It Works
Regular Expression: Uses a regular expression to match endpoint URLs within scripts.  
Recursive Fetching: Fetches script content recursively up to a specified depth (default is 3) to ensure deeper analysis.  
Result Display: Extracted URLs are collected in a Set to ensure uniqueness and then written to the document for easy viewing.  
## Customization
Recursion Depth: Adjust the recursion depth by modifying the if(depth>3)return; line in the bookmarklet code.  
Timeout Duration: Change the timeout duration by modifying the setTimeout(writeResults, 5000); line to allow more or less time for script fetching.
## Limitations
CORS Restrictions: Some scripts may not be fetchable due to cross-origin resource sharing (CORS) policies.
Depth Limit: The recursion depth is limited to prevent infinite loops, but this can be adjusted if needed.
Browser Compatibility: The bookmarklet is designed to work in modern browsers with JavaScript and async/await enabled.
## Contributing
Please contribute to the original author [0dayCTF](https://github.com/0dayCTF/endlets)
