# Surrogate Application AI Summary - Setup Instructions

This guide will help you set up the AI summary system for your surrogate applications in Zoho Creator.

---

## What You'll Get

✅ **Automatic summaries** on new application submissions
✅ **"Regenerate Summary" button** to update existing records
✅ **Auto-categorization** (Gold Star, Under Review, New Application)
✅ **Flag detection** (Medical Concerns, Background Concerns, Preterm History)

---

## Part 1: Add the On Submit Workflow

This makes summaries generate automatically for NEW applications.

### Steps:

1. **Open Zoho Creator** and go to your application
2. Navigate to **Forms** → **Full_Surrogate_Application** (or your form name)
3. Click **Edit Form**
4. Go to **Workflow** tab at the top
5. Click **On Submit** (or create it if it doesn't exist)
6. **Delete any existing code** in that workflow
7. **Copy and paste** the entire contents of `zoho-on-submit-script.txt`
8. Click **Save**

### What it does:
- Runs when someone submits a NEW application
- Skips if the summary already exists (prevents running on edits)
- Calls the Vercel AI endpoint
- Populates the `Candidate_Summary` field
- Sets `Application_Status` and `Application_Flags`

---

## Part 2: Add the Regenerate Function

This lets you manually regenerate summaries for existing records.

### Steps:

1. In Zoho Creator, go to **Functions** in the left sidebar
2. Click **New Function**
3. Name it: `regenerateSummary`
4. **Important:** Set the parameter:
   - Parameter Name: `recordID`
   - Parameter Type: `Number`
5. **Copy and paste** the entire contents of `zoho-regenerate-summary-function.txt`
6. **Update these two lines** at the top of the function:
   ```deluge
   appName = "alcea-surrogacy";  // Change to YOUR app link name
   formName = "Full_Surrogate_Application";  // Change to YOUR form name
   ```
7. Click **Save**

### How to find your app link name:
- Go to your Zoho Creator dashboard
- Look at the URL: `https://creator.zoho.com/yourname/YOUR-APP-NAME/`
- Use whatever is in place of `YOUR-APP-NAME`

---

## Part 3: Add a "Regenerate Summary" Button

This creates a button users can click to regenerate the summary.

### Option A: Button on the Form (Edit View)

1. Edit your **Full_Surrogate_Application** form
2. Drag a **Button** field onto the form
3. Set button properties:
   - **Label:** "🔄 Regenerate Summary"
   - **Type:** Custom Action
   - **On Click:** Select the `regenerateSummary` function
   - **Pass Parameter:** Select the record ID field
4. Save the form

### Option B: Button on a Report (List View)

1. Go to **Reports** → Create or edit a report showing applications
2. Click **Actions** → **Add Custom Action**
3. Configure:
   - **Action Name:** "Regenerate Summary"
   - **Function:** Select `regenerateSummary`
   - **Pass:** Record ID
   - **Display:** Show as button
4. Save the report

---

## Part 4: Verify Required Fields Exist

Make sure your form has these fields (or add them):

### Multi-line Text Field:
- **Field Name:** `Candidate_Summary`
- **Type:** Multi-line text
- **Suggested:** Make it read-only so users can't edit it

### Multi-select Field:
- **Field Name:** `Application_Flags`
- **Type:** Multi Select
- **Options:**
  - Medical Concerns
  - Background Concerns
  - Preterm History

### Dropdown Field:
- **Field Name:** `Application_Status`
- **Type:** Dropdown
- **Options:**
  - 🆕 New Application
  - ⭐ Gold Star - Fast Track
  - 🔍 Under Review
  - ✅ Approved for Matching
  - 📞 Contacted
  - 🤝 In Process
  - ⏸️ On Hold
  - ❌ Declined
  - 📁 Archived

---

## Part 5: Test It!

### Test New Submission:
1. Fill out and submit a test application
2. After submission, check the record
3. The `Candidate_Summary` field should be populated
4. The `Application_Status` should be set automatically

### Test Regeneration:
1. Open an existing application record
2. Click the "🔄 Regenerate Summary" button
3. Wait a few seconds
4. Refresh the page - the summary should update

---

## Troubleshooting

### Summary isn't generating on new submissions:

1. **Check the workflow is enabled:**
   - Go to Forms → Your Form → Workflow → On Submit
   - Make sure it's enabled (toggle should be ON)

2. **Check the logs:**
   - After submitting, go to **Settings** → **Logs** → **Script Logs**
   - Look for error messages or "AI Summary generated successfully"

3. **Verify the webhook URL is correct:**
   - In the script, line ~16 should be:
   ```deluge
   webhookUrl = "https://surrogate-application-processor.vercel.app/api/process-application";
   ```

### Regenerate button doesn't work:

1. **Check you updated the function parameters:**
   - Open the `regenerateSummary` function
   - Lines 13-14 should have YOUR app and form names

2. **Check button is passing record ID:**
   - Edit the button
   - Make sure it's passing the correct ID field

3. **Check function logs:**
   - Go to **Settings** → **Logs** → **Script Logs**
   - Look for the regenerate function execution

### Getting timeout errors:

The AI processing can take 10-30 seconds. This is normal! Zoho might show a timeout but the summary will still generate. Just refresh the page after a few seconds.

---

## Summary of Files

| File | Purpose | Where to Use |
|------|---------|--------------|
| `zoho-on-submit-script.txt` | Auto-generate summaries on new submissions | Form → Workflow → On Submit |
| `zoho-regenerate-summary-function.txt` | Regenerate summaries for existing records | Functions → New Function |
| `zoho-test-function.txt` | Test the webhook without submitting a form | Functions → For testing only |

---

## Need Help?

- The Vercel endpoint is already deployed and working: ✅
- Check Zoho Script Logs for detailed error messages
- Make sure all field names in the scripts match your actual field names in Zoho

---

## What's Next?

Once everything is working:
- Test with real applications
- Monitor the logs for the first few submissions
- Adjust the AI prompt if needed (in the Vercel function)
- Consider setting up email notifications for Gold Star applicants

The system is now ready to automatically process and summarize surrogate applications! 🎉
