# Nodejs Error: Can't set headers after they are sent

在使用 `express` 开发的时候出现这个错误:
**Error: Can't set headers after they are sent**
问题原因
在网上查找发现是由于在一次请求后服务器返回两次或者两次以上的响应，这个问题很容易出现在异步回调过程中

解决方案
在每次的请求处理中，一旦服务器返回响应，就及时的使用return，避免出现一次请求后，返回多次响应

```javascript
if (err) {
   logger.error('delete blog error', err);
   return res.json({
      success: 'false',
      message: err.message
   });
} else {
   res.json({
        success: 'true',
        data: blog
   })
}
```

