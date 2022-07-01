# 操作Email

名词解释

- MUA(Mail User Agent) 邮件用户代理，比如：OutLook，FoxMail等
- MTA(Mail Transfer Agent) 邮件传输代理
- MDA(Mail Delivery Agent) 邮件投递代理

历程

1. 发件人，比如：me@qq.com
2. MUA
3. 多级MTA：腾讯MTA =》其它中间MTA =》新浪MTA
4. MDA
5. MUA
6. 收件人，比如：you@sina.com

协议

- SMTP(Simple Mail Transfer Protocol) 发送协议
- POP(Post Office Protocol)，目前版本为：POP3 接收协议
- IMAP(Internet Message Access Protocol)，目前版本为：IMAP4 接收协议

> IMAP的优点是不但能取邮件，还可以直接操作MDA上存储的邮件，比如从收件箱移到垃圾箱等

Python库

> 参考文章：<https://mp.weixin.qq.com/s/NL5666lUq9m7_MiL1jmRmw>

- smtplib 内置
- Zmail 更简单
- yagmail 更优雅
