# Setup notes and prototype limitations

## Sanitized configuration

The assistant's function URLs point to `https://example.invalid/account` or `https://example.invalid/faq`. Its voice identifier is a placeholder. Make webhook and connection references are set to `0`, document/spreadsheet IDs are placeholders, and per-module restore metadata containing account/resource references has been removed. Module IDs, mappings, prompts, and export dates are preserved.

The sanitized blueprints are reference examples. Import compatibility has not been tested. They need new webhook registrations, provider connections, and resources in an isolated test workspace before they can run. Do not reconnect them to the original resources or real customer accounts.

## Workflow wiring

1. The Vapi assistant uses Deepgram `nova-2`, OpenAI `gpt-3.5-turbo`, and an ElevenLabs voice.
2. Eight account functions send a `Username` and `Column_Name` to the account webhook. The scenario queries Google Sheets using the username and returns columns A–I as newline-delimited text. `Column_Name` is not used by the supplied scenario.
3. The `Ask` function sends `Question` to the FAQ webhook. Make reads a Google document, supplies its text as model context, and returns the completion through the webhook response.
4. The Google Sheet's columns are Username, Name, Phone_Number, Password, Watch_History, Wishlist, Favorite_Genres, Subscription_Plan, and Email_Id.

These exports use the `message.functionCall.parameters` payload format. Provider compatibility and model availability have not been checked against current services.

The author recalls building the project around June 2024. The assistant configuration was exported on October 5, 2026; its export timestamp is preserved.

## Account verification boundary

The assistant prompt requests a username and password and instructs the model to compare them. The account scenario itself filters only on username and returns the entire row, including the password. It has no visible backend authorization or authenticated-session check. Prompt instructions do not establish server-enforced authentication.

The spreadsheet query interpolates the supplied username. Input validation/escaping is not visible in the blueprint. The shared account endpoint also makes every returned field available to the assistant regardless of which field was requested.

Before any real-account deployment, replace this demo flow with server-enforced authentication and authorization, validate external input, return only authorized fields, and keep password values out of tool responses and model context. Do not use spoken or spreadsheet-stored plaintext passwords for real accounts.

## FAQ and recording boundary

The FAQ prompt requests document-only answers, but the workflow does not validate grounding or provide evidence citations. Pricing and policies in the recorded demo are sample content, not current service advice. Refresh and verify the support source before reuse.

The supplied assistant configuration enables call recording. Any future deployment needs a deliberate recording/consent/retention policy before handling real callers.

## Validation performed

- Parsed the three supplied JSON exports.
- Matched assistant functions to their two Make workflow branches.
- Compared demonstrated behavior against a machine-generated transcript of the video.
- Checked sanitized output for original endpoints and resource/connection references.

No live Vapi calls, Make executions, provider-account setup, failed-login testing, or production-readiness assessment was performed.
