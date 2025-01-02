# Chrome-Extension-for-WhatsApp-CRM-and-Broadcasting
create a Chrome extension that integrates with WhatsApp for CRM and broadcasting purposes. This extension should allow users to efficiently manage contacts, send bulk messages, and streamline communication.
---------------
Creating a Chrome extension that integrates with WhatsApp for CRM (Customer Relationship Management) and broadcasting purposes involves using the WhatsApp Web client, automating the messaging process, and managing contacts effectively. Here's a breakdown of the steps to achieve this:
Features:

    Manage Contacts: Save and organize contact numbers or groups.
    Send Bulk Messages: Send messages to multiple contacts or groups at once.
    Streamline Communication: Automate the message sending process with custom text and dynamic data.
    Integrate WhatsApp Web: Interact with WhatsApp Web's UI via the extension.

Components:

    Popup UI: A user interface to configure settings, select contacts, and send messages.
    Background Script: To manage sending messages and fetching contact lists from WhatsApp Web.
    Content Script: To interact with the WhatsApp Web page to send messages.
    Permissions: Access WhatsApp Web and the active tab.

Steps:

    Create the Manifest File.
    Develop the Popup UI.
    Write the Background Script.
    Write the Content Script to interact with WhatsApp Web.

Here’s the complete code for the extension:
1. manifest.json - The Chrome Extension Manifest

{
  "manifest_version": 3,
  "name": "WhatsApp CRM Extension",
  "description": "Manage contacts, send bulk messages, and streamline communication on WhatsApp Web.",
  "version": "1.0",
  "permissions": [
    "activeTab",
    "storage"
  ],
  "background": {
    "service_worker": "background.js"
  },
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  "content_scripts": [
    {
      "matches": ["https://web.whatsapp.com/*"],
      "js": ["content.js"]
    }
  ],
  "icons": {
    "16": "icons/icon16.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  }
}

2. background.js - Handle Logic for Sending Messages

// background.js

chrome.runtime.onInstalled.addListener(() => {
  // Create a message to be used for bulk sending
  chrome.storage.local.set({ bulkMessage: "" });
});

// Listen for bulk message request from the popup
chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
  if (request.action === "sendBulkMessage") {
    sendMessageToContacts(request.contacts, request.message);
  }
});

// Function to send messages to contacts
function sendMessageToContacts(contacts, message) {
  contacts.forEach(contact => {
    // Send the message to WhatsApp Web for each contact (or group)
    chrome.scripting.executeScript({
      target: { tabId: sender.tab.id },
      func: sendMessageToWhatsApp,
      args: [contact, message]
    });
  });
}

// Function to interact with WhatsApp Web page and send messages
function sendMessageToWhatsApp(contact, message) {
  const contactElement = document.querySelector(`[title='${contact}']`);
  
  if (contactElement) {
    contactElement.click();
    
    setTimeout(() => {
      const messageBox = document.querySelector('[contenteditable="true"]');
      if (messageBox) {
        messageBox.textContent = message;
        const sendButton = document.querySelector('span[data-icon="send"]');
        sendButton.click();
      }
    }, 1000);
  }
}

3. content.js - Interact with WhatsApp Web

// content.js

// Listen for the extension to send messages to WhatsApp Web
chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
  if (request.action === "sendMessageToWhatsApp") {
    const contact = request.contact;
    const message = request.message;
    
    // Locate the contact and send the message
    const contactElement = document.querySelector(`[title='${contact}']`);
    if (contactElement) {
      contactElement.click();
      
      setTimeout(() => {
        const messageBox = document.querySelector('[contenteditable="true"]');
        if (messageBox) {
          messageBox.textContent = message;
          const sendButton = document.querySelector('span[data-icon="send"]');
          sendButton.click();
        }
      }, 1000);
    }
  }
});

4. popup.html - User Interface for the Extension

<!DOCTYPE html>
<html>
  <head>
    <title>WhatsApp CRM</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        padding: 10px;
        width: 300px;
      }
      input[type="text"], textarea {
        width: 100%;
        padding: 5px;
        margin: 10px 0;
      }
      button {
        background-color: #25D366;
        color: white;
        padding: 10px;
        border: none;
        width: 100%;
        cursor: pointer;
      }
      button:hover {
        background-color: #128C7E;
      }
    </style>
  </head>
  <body>
    <h3>WhatsApp CRM</h3>
    
    <label for="message">Message:</label>
    <textarea id="message" rows="5"></textarea>
    
    <label for="contacts">Contacts (comma separated):</label>
    <input type="text" id="contacts" />
    
    <button id="sendBulk">Send Bulk Message</button>
    
    <script>
      document.getElementById('sendBulk').addEventListener('click', () => {
        const message = document.getElementById('message').value;
        const contacts = document.getElementById('contacts').value.split(',').map(contact => contact.trim());
        
        // Send message to contacts via background script
        chrome.runtime.sendMessage({
          action: "sendBulkMessage",
          contacts: contacts,
          message: message
        });
      });
    </script>
  </body>
</html>

5. icons Folder

Place icon images with the following names:

    icon16.png
    icon48.png
    icon128.png

How the Extension Works:

    Popup Interface:
        Users enter a message and a list of contacts (comma-separated) into the popup.
        The extension allows users to select multiple contacts and send them a bulk message by clicking "Send Bulk Message."

    Message Sending:
        When the "Send Bulk Message" button is clicked, the message is sent to all selected contacts on WhatsApp Web.
        The extension uses the content.js script to interact with the WhatsApp Web page and send the messages to each contact.

    Automating WhatsApp Web:
        The extension uses chrome.scripting.executeScript() to run scripts that find contact names (or groups) and simulate typing the message in the message box.
        It also handles clicking the "Send" button to send the message.

    Background Script:
        The background script listens for the user's action and sends messages to all the specified contacts, ensuring that the extension is working across different tabs.

Installation:

    Save the code in a folder.
    Open chrome://extensions/ in Chrome.
    Enable "Developer Mode" and click "Load unpacked."
    Select the folder with the extension files.

Usage:

    Open WhatsApp Web.
    Click on the extension icon in the toolbar to open the popup.
    Add contacts (comma-separated) and a message.
    Click "Send Bulk Message" to send the message to all contacts.

Security and Privacy Considerations:

    Data Handling: No personal data is stored; everything happens within the WhatsApp Web session.
    Permissions: Ensure you use only the necessary permissions to avoid misuse. This extension requires the "activeTab" permission to interact with WhatsApp Web.

Conclusion:

This Chrome extension helps automate WhatsApp communication for CRM purposes by allowing users to send bulk messages easily. You can extend this further by adding features like managing contact groups, scheduling messages, and integrating with external CRM systems.
