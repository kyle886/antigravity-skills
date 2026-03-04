---
name: analytics-integration
description: Integrate Google Analytics 4 (GA4) and custom event tracking. Use when adding analytics, conversion tracking, or user behavior monitoring to web applications.
---

# Analytics Integration

Google Analytics 4 (GA4) and custom event tracking implementation.

## Use this skill when

- Adding analytics to web applications
- Tracking user interactions and conversions
- Implementing custom event tracking
- Setting up SIOR lead capture analytics

## Do not use this skill when

- Server-side analytics only
- Privacy-restricted environments

## Instructions

1. Set up GA4 property
2. Install tracking code
3. Define custom events
4. Configure goals/conversions

## Core Patterns

### 1. GA4 Basic Setup

```html
<!-- Global Site Tag (gtag.js) -->
<script
  async
  src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"
></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag() {
    dataLayer.push(arguments);
  }
  gtag("js", new Date());
  gtag("config", "G-XXXXXXXXXX");
</script>
```

### 2. Event Tracking Wrapper

```javascript
const Analytics = {
  trackingId: null,
  enabled: true,
  debug: false,

  init(trackingId, options = {}) {
    this.trackingId = trackingId;
    this.debug = options.debug || false;
    this.enabled = options.enabled !== false;

    if (this.debug) {
      console.log("[Analytics] Initialized with ID:", trackingId);
    }
  },

  event(eventName, params = {}) {
    if (!this.enabled || typeof gtag === "undefined") {
      if (this.debug)
        console.log("[Analytics] Event (disabled):", eventName, params);
      return;
    }

    gtag("event", eventName, params);

    if (this.debug) {
      console.log("[Analytics] Event:", eventName, params);
    }
  },

  pageView(pagePath, pageTitle) {
    this.event("page_view", {
      page_path: pagePath,
      page_title: pageTitle,
    });
  },

  setUserProperty(name, value) {
    if (typeof gtag !== "undefined") {
      gtag("set", "user_properties", { [name]: value });
    }
  },
};
```

### 3. SIOR-Specific Events

```javascript
const SIORAnalytics = {
  // Globe interactions
  trackGlobeRotation() {
    Analytics.event("globe_interaction", {
      event_category: "engagement",
      event_label: "rotation",
    });
  },

  trackMarketClick(marketId, marketName, region) {
    Analytics.event("market_selected", {
      event_category: "engagement",
      market_id: marketId,
      market_name: marketName,
      region: region,
    });
  },

  trackMarketView(marketId, duration) {
    Analytics.event("market_viewed", {
      event_category: "engagement",
      market_id: marketId,
      view_duration_seconds: Math.round(duration / 1000),
    });
  },

  // PDF exports
  trackPDFDownload(marketId, fieldCount, isMember) {
    Analytics.event("pdf_download", {
      event_category: "conversion",
      market_id: marketId,
      field_count: fieldCount,
      is_member: isMember,
    });
  },

  // Lead capture
  trackLeadSubmission(marketId, hasCompany) {
    Analytics.event("lead_submitted", {
      event_category: "conversion",
      market_id: marketId,
      has_company: hasCompany,
    });
  },

  trackLeadFormOpen(marketId) {
    Analytics.event("lead_form_opened", {
      event_category: "engagement",
      market_id: marketId,
    });
  },

  // Member actions
  trackMemberLogin() {
    Analytics.event("member_login", {
      event_category: "auth",
    });
  },

  trackCustomReportGenerated(marketCount, fieldCount) {
    Analytics.event("custom_report_generated", {
      event_category: "conversion",
      market_count: marketCount,
      field_count: fieldCount,
    });
  },
};
```

### 4. E-commerce Tracking (for future)

```javascript
// Track when user shows interest in premium features
function trackBeginCheckout() {
  gtag("event", "begin_checkout", {
    currency: "USD",
    value: 199.0,
    items: [
      {
        item_id: "sior-premium-annual",
        item_name: "SIOR Premium Membership",
        price: 199.0,
        quantity: 1,
      },
    ],
  });
}
```

### 5. Custom Dimensions

```javascript
// Set custom dimensions for user segmentation
function setUserContext(context) {
  gtag("set", "user_properties", {
    user_type: context.isMember ? "member" : "public",
    member_tier: context.memberTier || "none",
    company_size: context.companySize || "unknown",
    primary_market: context.primaryMarket || "unknown",
  });
}
```

### 6. Session Recording Integration

```javascript
// Optional: Integrate with session recording tools
const SessionRecording = {
  init(config) {
    if (config.hotjar) {
      this.initHotjar(config.hotjar.siteId, config.hotjar.version);
    }
    if (config.clarity) {
      this.initClarity(config.clarity.projectId);
    }
  },

  initHotjar(siteId, version) {
    (function (h, o, t, j, a, r) {
      h.hj =
        h.hj ||
        function () {
          (h.hj.q = h.hj.q || []).push(arguments);
        };
      h._hjSettings = { hjid: siteId, hjsv: version };
      a = o.getElementsByTagName("head")[0];
      r = o.createElement("script");
      r.async = 1;
      r.src = t + h._hjSettings.hjid + j + h._hjSettings.hjsv;
      a.appendChild(r);
    })(window, document, "https://static.hotjar.com/c/hotjar-", ".js?sv=");
  },

  initClarity(projectId) {
    (function (c, l, a, r, i, t, y) {
      c[a] =
        c[a] ||
        function () {
          (c[a].q = c[a].q || []).push(arguments);
        };
      t = l.createElement(r);
      t.async = 1;
      t.src = "https://www.clarity.ms/tag/" + i;
      y = l.getElementsByTagName(r)[0];
      y.parentNode.insertBefore(t, y);
    })(window, document, "clarity", "script", projectId);
  },
};
```

### 7. Privacy-Compliant Implementation

```javascript
const PrivacyCompliantAnalytics = {
  consentGiven: false,

  init(trackingId) {
    // Load script but don't activate until consent
    this.trackingId = trackingId;
    this.loadScript();
    this.checkStoredConsent();
  },

  loadScript() {
    const script = document.createElement("script");
    script.async = true;
    script.src = `https://www.googletagmanager.com/gtag/js?id=${this.trackingId}`;
    document.head.appendChild(script);

    window.dataLayer = window.dataLayer || [];
    window.gtag = function () {
      dataLayer.push(arguments);
    };
    gtag("js", new Date());

    // Default to denied consent
    gtag("consent", "default", {
      analytics_storage: "denied",
    });
  },

  checkStoredConsent() {
    const consent = localStorage.getItem("analytics_consent");
    if (consent === "granted") {
      this.grantConsent();
    }
  },

  grantConsent() {
    this.consentGiven = true;
    localStorage.setItem("analytics_consent", "granted");

    gtag("consent", "update", {
      analytics_storage: "granted",
    });

    gtag("config", this.trackingId);
  },

  denyConsent() {
    this.consentGiven = false;
    localStorage.setItem("analytics_consent", "denied");

    gtag("consent", "update", {
      analytics_storage: "denied",
    });
  },
};
```

## Event Schema for SIOR

| Event Name                | Category   | Parameters                        |
| ------------------------- | ---------- | --------------------------------- |
| `market_selected`         | engagement | market_id, market_name, region    |
| `market_viewed`           | engagement | market_id, view_duration_seconds  |
| `globe_interaction`       | engagement | interaction_type                  |
| `pdf_download`            | conversion | market_id, field_count, is_member |
| `lead_submitted`          | conversion | market_id, has_company            |
| `lead_form_opened`        | engagement | market_id                         |
| `member_login`            | auth       | -                                 |
| `custom_report_generated` | conversion | market_count, field_count         |

## Best Practices

### Do's

- Use consistent event naming (snake_case)
- Include meaningful parameters
- Respect user privacy/consent
- Test in GA4 DebugView

### Don'ts

- Don't track PII (emails, names)
- Don't spam events (batch interactions)
- Don't forget opt-out mechanism

## Resources

- [GA4 Developer Guide](https://developers.google.com/analytics/devguides/collection/ga4)
- [gtag.js Reference](https://developers.google.com/tag-platform/gtagjs/reference)
