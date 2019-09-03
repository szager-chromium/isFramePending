Answers to [Self-Review Questionnaire: Security and Privacy](https://www.w3.org/TR/security-privacy-questionnaire/)

### What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?

This API reveals the fact that the browser has scheduled an animation frame, indicating that the page contents are dirty. This information is inseparable from the API's primary purpose.

### Is this specification exposing the minimum amount of information necessary to power the feature?

Yes

### How does this specification deal with personal information or personally-identifiable information or information derived thereof?

N/A

### How does this specification deal with sensitive information?

N/A

### Does this specification introduce new state for an origin that persists across browsing sessions?

No

### What information from the underlying platform, e.g. configuration data, is exposed by this specification to an origin?

None

### Does this specification allow an origin access to sensors on a user’s device

No

### What data does this specification expose to an origin? Please also document what data is identical to data exposed by other features, in the same or different contexts.

N/A

### Does this specification enable new script execution/loading mechanisms?

No

### Does this specification allow an origin to access other devices?

No

### Does this specification allow an origin some measure of control over a user agent’s native UI?

No

### What temporary identifiers might this this specification create or expose to the web?

None

### How does this specification distinguish between behavior in first-party and third-party contexts?

N/A

### How does this specification work in the context of a user agent’s Private \ Browsing or "incognito" mode?

No difference

### Does this specification have a "Security Considerations" and "Privacy Considerations" section?

The [explainer](./explainer.md#security-considerations) has such a section.

### Does this specification allow downgrading default security characteristics?

No
