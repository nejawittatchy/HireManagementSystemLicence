# DeranCab Licence file format

This file describes the JSON structure stored in `DeranCabLicence` which your desktop app can read.

Top-level schema (JSON):

- license_type: string — "Full" or "Trial"
- license_key: string — opaque key for display/verification
- issued_to: string — customer or company name
- issued_by: string — issuer name (e.g., "DeranCab")
- issued_date: ISO-8601 timestamp (UTC recommended)
- activation_date: ISO-8601 timestamp when license becomes active
- expiry_date: ISO-8601 timestamp when license expires (null for perpetual)
- trial_duration_days: integer — helpful for trial licenses (optional)
- time_restrictions: object — optional daily/time window restrictions
  - allowed_from, allowed_to: HH:MM (24h)
  - timezone: tz string (e.g., "UTC")
  - allowed_days: array of 3-letter days (mon,tue,...)
- limits: object — e.g., max_installs, max_concurrent_users
- features: object — feature flags and full_access boolean
- hardware_binding: object — optional machine_id or mac_addresses array
- notes: string — human readable notes
- signature: string|null — optional digital signature for tamper detection
- metadata: object — format_version and file_type

- expired_message: string — optional user-facing message shown when the license is expired or a trial has ended. If present, the desktop app should display this message to the user (fall back to a default friendly message if not provided).

Notes for the desktop app
- Parse the file as UTF-8 JSON. If parsing fails, treat the license as invalid.
- Validate required fields: `license_type`, `license_key`, `issued_date`, `activation_date`.
- If `expiry_date` is present and now > expiry_date, license is expired.
- For `Trial` licenses, prefer to validate `trial_duration_days` in combination with `activation_date`.
 - If the license is expired (trial or expiry_date passed) and `expired_message` exists, display it to the user. Suggested default message if none provided: "Your trial has expired. Please purchase a full license to continue using DeranCab or contact support@derancab.example."
- If `hardware_binding.machine_id` or `mac_addresses` is set, verify against the current machine.
- If `signature` is used, verify signature before trusting fields.

Example

See the `DeranCabLicence` file next to this README for an example of a Trial license.

Versioning
- Start with format_version "1.0". If the schema changes in future, increment the format_version and document changes here.

Security
- Keep license files readable only by the application where possible. Consider storing signatures and verifying them to prevent tampering.

Suggested user-facing behavior when a trial expires
- Show `expired_message` if present. Keep message actionable: include a purchase link or contact email.
- Provide a prominent CTA (purchase or enter license key) and a way to copy the support email or open the user's browser to the purchase URL.
- Do not expose internal license metadata in the UI.
