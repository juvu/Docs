# Q1: Nginx several servers, but only localhost and 127.0.0.1 can use

# A:the domain has bound with 127.0.0.1 in Anjuke's internat. So the host machine can access by servername without edit hosts file.

--------------------------------------------------------------------------------

# Q2: vagrant客户机配置好docker后，无法挂载共享文件夹

jsonp使用post方法405错误

```html
<script src="http://platform.cdn.xianyugame.com/platform/common/js/jquery-1.11.0.min.js">
</script>

<script>
  $.ajax(
  {
      type:'get',
      url : 'https://actsapi.xianyugame.com/games/preorder?type=1&number=18817263572',
      // data: data,
      dataType : 'jsonp',
      crossDomain: true,
      jsonp:"jsoncallback",
      success  : function(data) {
          console.log(data);
      },
      error : function() {
          console.log(data.msg);
      }
  });
</script>
```
