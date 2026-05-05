# HTML Display Ad Baseline Template
IAB HTML5 | 2024 Guidelines | Last verified: 2026-04-27

---

## How to use this file
1. Copy the template below
2. Fill in the CONFIG BLOCK at the top with your ad dimensions and click URL
3. Replace all `[PLACEHOLDER]` values with real content
4. Test the click-through before submitting to publisher

---

## IAB Standard Sizes (quick reference)

| Name | Dimensions | Notes |
|---|---|---|
| Medium Rectangle | 300×250 | Most widely accepted |
| Leaderboard | 728×90 | Top of page placement |
| Half Page | 300×600 | High impact |
| Large Rectangle | 336×280 | Good engagement |
| Skyscraper | 160×600 | Sidebar |
| Billboard | 970×250 | High impact desktop |

Max file size: 200KB for all formats. Max 15 initial HTTP requests.

---

## Ad Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Ad Name] — [300x250]</title>

  <!--
    =====================================================
    CONFIG BLOCK — edit this section before anything else
    =====================================================
    width:      ad width in pixels (match IAB spec)
    height:     ad height in pixels (match IAB spec)
    clickTag:   leave as empty string — publisher injects the real URL.
                For testing only, set a local URL.
    =====================================================
  -->
  <script>
    // clickTag — required by all major ad networks (Google Display Network, DoubleClick, etc.)
    // Publishers replace this with the real click-through URL at serving time.
    // NEVER hardcode the destination URL directly in the <a href> below — always use window.clickTag.
    var clickTag = "";
  </script>

  <style>
    /*
      Reset and container — all CSS is inline or in this <style> block.
      No external stylesheets. Ad must be self-contained.
    */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      width: 300px;   /* ← match CONFIG width */
      height: 250px;  /* ← match CONFIG height */
      overflow: hidden;
      background-color: #ffffff;
      font-family: Arial, Helvetica, sans-serif;
    }

    /*
      Ad container — must match body dimensions exactly.
      Border is optional but helps during development to see boundaries.
    */
    #ad-container {
      width: 300px;   /* ← match CONFIG width */
      height: 250px;  /* ← match CONFIG height */
      position: relative;
      overflow: hidden;
      border: 1px solid #cccccc; /* remove before submitting to publisher if not wanted */
      cursor: pointer;
    }

    /* Background / hero image */
    #ad-bg {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: #003366;  /* fallback if image fails to load */
      background-image: url('[ad-background.jpg]');  /* inline images preferred; external counts toward 15-request limit */
      background-size: cover;
      background-position: center;
    }

    /* Content layer — sits on top of background */
    #ad-content {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: 20px;
    }

    /* Logo */
    #ad-logo {
      display: block;
      max-width: 120px;
      height: auto;
    }

    /* Headline */
    #ad-headline {
      font-size: 22px;
      font-weight: bold;
      color: #ffffff;
      line-height: 1.2;
      text-shadow: 0 1px 3px rgba(0,0,0,0.5);
    }

    /* Subheadline (optional) */
    #ad-subline {
      font-size: 13px;
      color: #ffffff;
      margin-top: 6px;
      opacity: 0.9;
    }

    /* CTA button */
    #ad-cta {
      display: inline-block;
      background-color: #ff6600;
      color: #ffffff;
      font-size: 13px;
      font-weight: bold;
      padding: 8px 18px;
      border-radius: 3px;
      text-transform: uppercase;
      letter-spacing: 0.5px;
      width: fit-content;
    }

    /* Legal / disclaimer text */
    #ad-legal {
      font-size: 9px;
      color: rgba(255,255,255,0.7);
      line-height: 1.3;
    }
  </style>
</head>
<body>

  <!--
    Click wrapper — the entire ad is clickable.
    onclick uses window.clickTag (the variable declared in the CONFIG BLOCK above).
    Never hardcode the URL here — publishers replace clickTag at serving time.
    href="#" prevents navigation if JavaScript is disabled.
  -->
  <a id="ad-container"
     href="#"
     onclick="window.open(window.clickTag); return false;"
     title="[Ad title for screen readers / accessibility]">

    <!-- Background layer -->
    <div id="ad-bg"></div>

    <!-- Content layer -->
    <div id="ad-content">

      <!-- Logo — use absolute URL if external, or inline SVG / base64 for self-contained -->
      <img id="ad-logo"
           src="[https://your-domain.com/logo.png]"
           alt="[Company Name]">
      <!-- Note: external image = 1 of your 15 HTTP request allowance -->

      <!-- Headline area -->
      <div>
        <div id="ad-headline">[Main Headline — short and punchy]</div>
        <div id="ad-subline">[Supporting line — optional]</div>
      </div>

      <!-- Bottom area: CTA + legal -->
      <div>
        <div id="ad-cta">[Call to Action]</div>
        <div id="ad-legal">[Legal disclaimer if required — terms apply, etc.]</div>
      </div>

    </div><!-- /ad-content -->

  </a><!-- /ad-container -->

</body>
</html>
```

---

## Animated Version (CSS animation only — no JavaScript required)

Add this inside the `<style>` block for a simple entrance animation:

```css
/* Fade-in animation — CSS only, no JavaScript needed */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to   { opacity: 1; transform: translateY(0); }
}

#ad-headline {
  animation: fadeIn 0.6s ease-out 0.3s both;
}

#ad-cta {
  animation: fadeIn 0.6s ease-out 0.8s both;
}

/*
  IAB rule: animation must stop after 30 seconds (or 3 loops, whichever is shorter).
  For looping animations, use: animation-iteration-count: 3;
  For a single play: animation-iteration-count: 1; (default)
*/
```

---

## Checklist Before Submitting to Publisher

- [ ] `var clickTag = "";` is present (empty string — publisher fills it in)
- [ ] `window.open(window.clickTag)` used in click handler (not a hardcoded URL)
- [ ] File size under 200KB total (all assets combined)
- [ ] HTTP requests counted: images + scripts ≤ 15 initial requests
- [ ] Ad dimensions match `body` width/height AND `#ad-container` width/height
- [ ] No audio autoplay
- [ ] Animation stops after 30 seconds (or 3 loops) if animated
- [ ] `title` attribute on the `<a>` wrapper describes the ad destination
- [ ] Tested click-through: click opens the correct URL
- [ ] Tested in Chrome and Firefox before delivery
