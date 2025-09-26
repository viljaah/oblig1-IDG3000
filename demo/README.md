how to start:
1. cd demo/broken || cd demo/fixed
2. npx http-server . -p 8000 || npx http-server . -p 8001
3. go to: http://localhost:8000 || http://localhost:8001
4. click on the html page in chrome

what to observe for broken page: 
Network activity: Open browser DevTools (F12) → Network tab → reload the page
- You should see ALL images start downloading immediately
- Even images you can't see yet are being loaded


