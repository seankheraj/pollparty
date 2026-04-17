PollParty 🗳️

PollParty is a lightweight, real-time data visualization dashboard designed to transform static Google Forms into dynamic, live polling experiences. Built for educators, conference speakers, and workshop facilitators, it provides a seamless "bridge" between audience responses and professional live charts.

✨ Features

Real-Time Visualization: Instantly syncs with Google Forms to display live results as they come in.

Zero Database Setup: Leverages Google Apps Script as a serverless backend, eliminating the need for complex database management.

Customizable Dashboard: A clean, responsive UI built for high visibility during live presentations.

Dual-Link Integration: Dedicated spaces for the participant "View" link and the creator "Edit" link within the console.

Support for Multiple Question Types: Handles Multiple Choice, Checkboxes, and Dropdowns with automatic tallying.

🚀 How It Works

PollParty functions through a simple three-tier architecture:

Input: Your standard Google Form where participants submit data.

Bridge: A custom Google Apps Script (Web App) that parses form responses into a JSON feed.

Display: A frontend console that fetches that JSON feed and renders it using interactive charts.

🛠️ Built With

Frontend: HTML5, CSS3, JavaScript (ES6+)

Data Processing: Google Apps Script (GAS)

Styling: Modern, responsive CSS with a focus on presentation legibility

📖 Quick Start

To get your own version of PollParty running, follow these steps:

🛠 Step 1: Prepare Your Google Form

Create a Google Form with at least one Multiple Choice, Checkbox, or Dropdown question.

Go to the Responses tab and ensure the form is accepting responses.

Click the "Send" button and copy the View Form URL (the link you share with participants).

Keep the Edit Form URL (the URL in your browser while building the form) handy for the sidebar.

⚙️ Step 2: Set up the Data Connector

To send data to the console, you need a small "bridge" script:

Open your Google Form.

Click the three dots (⋮) in the top right and select Script editor.

Delete any code there and paste the following:

function doGet() {
  const form = FormApp.getActiveForm();
  const responses = form.getResponses();
  const items = form.getItems();
  
  const questionsData = items.map((item) => {
    const questionTitle = item.getTitle();
    const itemType = item.getType();
    let results = {};
    let responseCount = 0;

    responses.forEach(response => {
      const itemResponse = response.getResponseForItem(item);
      if (itemResponse) {
        const answer = itemResponse.getResponse();
        responseCount++;
        if (itemType === FormApp.ItemType.MULTIPLE_CHOICE || 
            itemType === FormApp.ItemType.LIST || 
            itemType === FormApp.ItemType.CHECKBOX) {
          if (Array.isArray(answer)) {
            answer.forEach(val => { results[val] = (results[val] || 0) + 1; });
          } else {
            results[answer] = (results[answer] || 0) + 1;
          }
        }
      }
    });

    return {
      title: questionTitle,
      results: results
    };
  });

  return ContentService.createTextOutput(JSON.stringify(questionsData))
    .setMimeType(ContentService.MimeType.JSON);
}


Click Deploy > New Deployment.

Select type: Web App.

Set "Who has access" to Anyone.

Copy the Web App URL (this is your "Script URL").

🚀 Step 3: Configure the Console

Open index.html in a text editor.

Find the POLL_CONFIG variable at the bottom of the file.

Replace the placeholder URLs with your own:

script: The Web App URL from Step 2.

form: The View Link for participants.

edit: The Edit Link for your convenience.

📄 License

This project is open-source and available under the GNU GPLv3 License.
