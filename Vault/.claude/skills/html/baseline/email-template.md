# HTML Email Baseline Template
HTML 4.01 | Table-based layout | Last verified: 2026-04-27

⚠ Gmail clips emails over 102KB. Keep total HTML under 100KB.
⚠ This is HTML 4.01 email HTML — NOT web HTML. Do not use HTML5 elements.

---

## How to use this file
1. Copy the template below into your email file
2. Replace all `[PLACEHOLDER]` values with real content
3. Test in Litmus or Email on Acid before sending
4. Keep total file size under 100KB (excluding hosted images)

---

## Email Template

```html
<!-- EMAIL HTML — not for web pages -->
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
  "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<!--
  HTML Email Template — table-based layout for cross-client compatibility.
  Compatible with: Gmail, Outlook 2016/2019/365, Apple Mail, iOS Mail, Android Gmail.

  RULES:
  - All CSS must be inline (style="...") — no external stylesheets, no <style> in <head>
    (Gmail strips <style> in <head>. Exception: media queries for mobile — keep in <head>)
  - No HTML5 semantic elements (<article>, <section>, <nav>, etc.)
  - No JavaScript
  - All images must use absolute URLs (https://...) — relative paths break in email
  - Max width: 600px (wider breaks in many clients)
  - No web fonts — Outlook ignores @font-face. Use system font stack.
-->
<html xmlns="http://www.w3.org/1999/xhtml" lang="en">
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Email Subject Line]</title>

  <!--
    Media queries for mobile — the one place where <style> in <head> is acceptable.
    Gmail supports these. Outlook Mobile does not — but Outlook Mobile uses a fixed layout anyway.
  -->
  <style type="text/css">
    @media only screen and (max-width: 600px) {
      .email-container { width: 100% !important; }
      .stack-on-mobile { display: block !important; width: 100% !important; }
    }
  </style>
</head>
<body style="margin: 0; padding: 0; background-color: #f4f4f4; -webkit-text-size-adjust: 100%; -ms-text-size-adjust: 100%;">

  <!--
    Outlook MSO wrapper — required for Outlook 2007-2019 to respect the 600px width.
    Without this, Outlook ignores max-width and stretches to full screen width.
  -->
  <!--[if mso]>
  <table width="600" cellspacing="0" cellpadding="0" border="0" align="center">
  <tr><td>
  <![endif]-->

  <!-- Email wrapper — set width here for all non-Outlook clients -->
  <table class="email-container" width="600" cellspacing="0" cellpadding="0" border="0"
         align="center" style="max-width: 600px; margin: 0 auto; background-color: #ffffff;">

    <!-- HEADER -->
    <tr>
      <td style="padding: 30px 30px 20px 30px; text-align: center; background-color: #ffffff;">
        <!--
          Logo — use absolute URL. Include width and height attributes.
          Always include alt text — many clients block images by default.
        -->
        <img src="https://[your-domain.com]/logo.png"
             alt="[Company Name]"
             width="180"
             height="60"
             style="display: block; margin: 0 auto; border: 0;">
      </td>
    </tr>

    <!-- HERO IMAGE (optional — remove if not needed) -->
    <tr>
      <td style="padding: 0;">
        <img src="https://[your-domain.com]/hero.jpg"
             alt="[Describe the image for screen readers]"
             width="600"
             height="300"
             style="display: block; width: 100%; border: 0;">
      </td>
    </tr>

    <!-- BODY COPY -->
    <tr>
      <td style="padding: 30px 30px 20px 30px;">

        <!-- Heading — use inline styles for all typography -->
        <h1 style="margin: 0 0 15px 0; font-family: Arial, Helvetica, sans-serif;
                   font-size: 26px; font-weight: bold; color: #333333; line-height: 1.3;">
          [Main Heading]
        </h1>

        <!-- Body text — system fonts only (no web fonts) -->
        <p style="margin: 0 0 15px 0; font-family: Arial, Helvetica, sans-serif;
                  font-size: 16px; color: #555555; line-height: 1.6;">
          [Body copy paragraph. Keep paragraphs short — emails are read quickly.
          Aim for under 3 sentences per paragraph.]
        </p>

        <p style="margin: 0 0 25px 0; font-family: Arial, Helvetica, sans-serif;
                  font-size: 16px; color: #555555; line-height: 1.6;">
          [Second paragraph if needed.]
        </p>

        <!-- CTA BUTTON — table-based for Outlook compatibility -->
        <!--
          Why a table for a button?
          Outlook ignores border-radius and padding on <a> tags.
          A table cell with background-color is the only reliable way
          to get a styled button that works in all Outlook versions.
        -->
        <table cellspacing="0" cellpadding="0" border="0">
          <tr>
            <td style="background-color: #0066cc; border-radius: 4px;">
              <a href="https://[your-domain.com]/[cta-link]"
                 style="display: inline-block; padding: 14px 28px;
                        font-family: Arial, Helvetica, sans-serif;
                        font-size: 16px; font-weight: bold;
                        color: #ffffff; text-decoration: none;">
                [Button Label]
              </a>
            </td>
          </tr>
        </table>

      </td>
    </tr>

    <!-- TWO-COLUMN SECTION (optional — remove if not needed) -->
    <!--
      Two columns using a nested table.
      On mobile: .stack-on-mobile class makes each column full width.
    -->
    <tr>
      <td style="padding: 0 30px 20px 30px;">
        <table width="100%" cellspacing="0" cellpadding="0" border="0">
          <tr>
            <!-- Column 1 -->
            <td class="stack-on-mobile" width="48%" valign="top"
                style="padding-right: 2%;">
              <h2 style="margin: 0 0 10px 0; font-family: Arial, Helvetica, sans-serif;
                         font-size: 18px; font-weight: bold; color: #333333;">
                [Column 1 Heading]
              </h2>
              <p style="margin: 0; font-family: Arial, Helvetica, sans-serif;
                        font-size: 15px; color: #555555; line-height: 1.6;">
                [Column 1 copy]
              </p>
            </td>
            <!-- Column 2 -->
            <td class="stack-on-mobile" width="48%" valign="top"
                style="padding-left: 2%;">
              <h2 style="margin: 0 0 10px 0; font-family: Arial, Helvetica, sans-serif;
                         font-size: 18px; font-weight: bold; color: #333333;">
                [Column 2 Heading]
              </h2>
              <p style="margin: 0; font-family: Arial, Helvetica, sans-serif;
                        font-size: 15px; color: #555555; line-height: 1.6;">
                [Column 2 copy]
              </p>
            </td>
          </tr>
        </table>
      </td>
    </tr>

    <!-- DIVIDER -->
    <tr>
      <td style="padding: 0 30px;">
        <table width="100%" cellspacing="0" cellpadding="0" border="0">
          <tr>
            <td style="border-bottom: 1px solid #eeeeee; padding: 0;">&nbsp;</td>
          </tr>
        </table>
      </td>
    </tr>

    <!-- FOOTER -->
    <tr>
      <td style="padding: 20px 30px 30px 30px; text-align: center;
                 background-color: #f9f9f9;">
        <p style="margin: 0 0 10px 0; font-family: Arial, Helvetica, sans-serif;
                  font-size: 13px; color: #999999; line-height: 1.5;">
          [Company Name] | [Address Line 1], [City, State, Postcode]
        </p>
        <p style="margin: 0; font-family: Arial, Helvetica, sans-serif;
                  font-size: 13px; color: #999999;">
          <!--
            Unsubscribe link is legally required in most jurisdictions (CAN-SPAM, GDPR, Australian Spam Act).
            Your ESP (Mailchimp, Campaign Monitor, etc.) typically inserts this automatically.
            Include it here as a placeholder.
          -->
          <a href="[unsubscribe-url]"
             style="color: #999999; text-decoration: underline;">Unsubscribe</a>
          &nbsp;|&nbsp;
          <a href="[view-in-browser-url]"
             style="color: #999999; text-decoration: underline;">View in browser</a>
        </p>
      </td>
    </tr>

  </table><!-- /email-container -->

  <!--[if mso]>
  </td></tr></table>
  <![endif]-->

</body>
</html>
```

---

## Checklist Before Sending

- [ ] Total HTML size under 100KB (open DevTools → Network → check document size)
- [ ] All images have `alt` text
- [ ] All image URLs are absolute (`https://...`)
- [ ] All CSS is inline (no external stylesheet linked)
- [ ] Unsubscribe link is present and points to a real URL
- [ ] Tested in Litmus or Email on Acid (minimum: Gmail, Outlook 2019, iOS Mail)
- [ ] Preview text set in your ESP (the line of text after the subject line in inbox)
- [ ] CTA button link is correct and opens the right page
- [ ] No JavaScript anywhere in the file
