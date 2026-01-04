# Google Sheets Integration Setup

To connect your form to a Google Sheet, follow these steps:

1.  **Create a Google Sheet**:
    *   Go to [sheets.google.com](https://sheets.google.com) and create a new blank spreadsheet.
    *   Name it "Rental Client Intake Data" (or whatever you prefer).

2.  **Open Apps Script**:
    *   In the spreadsheet, click on **Extensions** > **Apps Script**.

3.  **Add the Script**:
    *   Delete any code in the `Code.gs` file.
    *   Copy and paste the code below into the editor:

```javascript
/* 
 * Google Apps Script to Handle Form Submissions
 */

function doPost(e) {
  try {
    // 1. Get the sheet
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    
    // 2. Parse the incoming JSON data
    var data = JSON.parse(e.postData.contents);
    
    // 3. Define the headers (columns) we want
    var headers = [
      "timestamp", "firstName", "lastName", "phone", "email", 
      "preferredContact", "agent", "propertyInterest", "bedrooms", 
      "neighborhoods", "moveInDate", "budget", "nonNegotiables", 
      "hasPets", "catCount", "dogCount", "dogDetails", 
      "coapplicants", "evictions", "verifiableIncome", "creditScore", 
      "tourDays", "tourTimes", "notes"
    ];
    
    // 4. If the sheet is empty, add the headers first
    if (sheet.getLastRow() === 0) {
      sheet.appendRow(headers);
    }
    
    // 5. Map the data to the headers
    var row = headers.map(function(header) {
      return data[header] || "";
    });
    
    // 6. Add the new row
    sheet.appendRow(row);
    
    // 7. Return success
    return ContentService.createTextOutput(JSON.stringify({ "result": "success" }))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch (error) {
    // Return error if something goes wrong
    return ContentService.createTextOutput(JSON.stringify({ "result": "error", "error": error.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

4.  **Deploy as Web App**:
    *   Click the blue **Deploy** button > **New deployment**.
    *   Click the **Select type** gear icon > **Web app**.
    *   **Description**: Enter "Form Submission".
    *   **Execute as**: Select **Me** (your email).
    *   **Who has access**: Select **Anyone** (This is important! It allows the public form to send data).
    *   Click **Deploy**.
    *   You may need to "Authorize access". Login with your Google account.
        *   If you see "Google hasn’t verified this app", click **Advanced** -> **Go to (Untitled project) (unsafe)** (It is safe, it's your own code).
        *   Click **Allow**.

5.  **Copy the URL**:
    *   Copy the **Web app URL** that appears (starts with `https://script.google.com/macros/s/...`).

6.  **Update your Website**:
    *   Open `index.html`.
    *   Find the `GOOGLE_SCRIPT_URL` variable at the top of the script (I have added this for you).
    *   Paste your URL inside the quotes.
