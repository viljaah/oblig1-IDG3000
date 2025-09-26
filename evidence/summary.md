## broken version
1. node -e "const audit = JSON.parse(require('fs').readFileSync('evidence/audit-broken.json')); console.log('Total requests:', audit.audits['network-requests'].details.items.length)"
- Total requests: 23
2.  node -e "console.log('Performance score:', JSON.parse(require('fs').readFileSync('evidence/audit-broken.json')).categories.performance.score * 100)"
- Performance score: 56.99999999999999
3. node -e "console.log('LCP:', JSON.parse(require('fs').readFileSync('evidence/audit-broken.json')).audits['largest-contentful-paint'].numericValue + 'ms')"
- LCP: 3580.0414000000005ms

## fixed version
1.  node -e "console.log('Fixed LCP:', JSON.parse(require('fs').readFileSync('evidence/audit-fixed.json')).audits['largest-contentful-paint'].numericValue)"
- Fixed LCP: 2281.2519000000007
2.  node -e "const audit = JSON.parse(require('fs').readFileSync('evidence/audit-fixed.json')); const items = audit.audits['network-requests'].details.items; console.log('Fixed Total transfer:', items.reduce((sum, item) => sum + (item.transferSize || 0), 0))"
- Fixed Total transfer: 161759
3.  node -e "const audit = JSON.parse(require('fs').readFileSync('evidence/audit-fixed.json')); console.log('Fixed Total requests:', audit.audits['network-requests'].details.items.length)"
- Fixed Total requests: 21