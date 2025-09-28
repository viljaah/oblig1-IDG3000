## broken version
1. node -e "const audit = JSON.parse(require('fs').readFileSync('evidence/audit-broken-with-videos.json')); console.log('Total requests:', audit.audits['network-requests'].details.items.length)"
- Total requests: 23
2.  node -e "console.log('LCP:', JSON.parse(require('fs').readFileSync('evidence/audit-broken-with-videos.json')).audits['largest-contentful-paint'].numericValue + 'ms')"
- LCP: 11351.923200000001ms
3. node -e "const audit = JSON.parse(require('fs').readFileSync('evidence/audit-broken-with-videos.json')); const items = audit.audits['network-requests'].details.items; console.log('Total transfer:', items.reduce((sum, item) => sum + (item.transferSize || 0), 0))"
- Total transfer: 3726845 = 3.7MB


## fixed version
1.  ode -e "const audit = JSON.parse(require('fs').readFileSync('evidence/audit-fixed-with-videos.json')); const items = audit.audits['network-requests'].details.items; console.log('Fixed total transfer:', items.reduce((sum, item) => sum + (item.transferSize || 0), 0))"
- Fixed total transfer: 141960 = 147KB
2.  node -e "const audit = JSON.parse(require('fs').readFileSync('evidence/audit-fixed-with-videos.json')); console.log('Fixed total requests:', audit.audits['network-requests'].details.items.length)"
Fixed total requests: 18

## code for finding out lighthouse viewport settings
node -e "
const audit = JSON.parse(require('fs').readFileSync('evidence/audit-broken-with-videos.json'));
console.log('=== LIGHTHOUSE VIEWPORT SETTINGS ===');
console.log('Screen emulation:', JSON.stringify(audit.configSettings?.screenEmulation, null, 2));
console.log('Form factor:', audit.configSettings?.formFactor);
console.log('Throttling method:', audit.configSettings?.throttlingMethod);
"
=== LIGHTHOUSE VIEWPORT SETTINGS ===
Screen emulation: {
  "mobile": true,
  "width": 412,
  "height": 823,
  "deviceScaleFactor": 1.75,
  "disabled": false
}
Form factor: mobile
Throttling method: simulate


## Updated Results with Media Files Loading
### broken version 
- Total requests: 23
- LCP: 11,351ms  
- Total transfer: 3,726,845 bytes = 3.7MB

### fixed version  
- Total requests: 18
- Total transfer: 141,960 bytes = 142KB
- Videos/audio deferred with preload="none"


| Version | Transfer Size | Requests |
|---------|---------------|----------|
| Broken  | 3.7 MB       | 23       |
| Fixed   | 142 KB       | 18       |
| **Savings** | **3.58 MB (96% reduction)** | **5 fewer requests** |