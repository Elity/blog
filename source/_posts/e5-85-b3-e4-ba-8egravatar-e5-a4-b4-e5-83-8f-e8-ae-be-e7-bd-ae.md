---
title: 关于Gravatar头像设置
tags:
  - Gravatar
url: 604.html
id: 604
categories:
  - 网文网事
date: 2011-09-22 22:54:05
---

![](http://www.folaji.com/wp-content/uploads/2009/03/gravatar.jpg "1")

Globally Recognized Avatar的缩写,是 http://www.gravatar.com 推出的一项服务，意为“全球通用头像”。如果在Gravatar的服务器上放置了你自己的头像，那么在任何支持Gravatar的blog或者留言本上留言时，只要提供你与这个头像关联的email地址，就能够显示出你的Gravatar头像来。

Gravatar是通过邮件地址来识别头像的，所以就是说你只要填写你注册时候的邮件地址就可以了。ok，我们开始申请，在浏览器中输入[http://en.gravatar.com/signup](http://en.gravatar.com/signup)然后输入自己的邮件地址，提交后就可以进行设置了，当图片设置完成后只要选择G级就好了，因为一般情况下我们头像都不会太敏感的。

以下留给Wordpress博主

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

### wordpress博客gravatar头像被墙的解决办法

　　打开博客主目录下的wp-includes/pluggable.php，在第1702行左右找到如下代码：

if ( is_ssl() ) {

$host = 'https://secure.gravatar.com';

        } else {

        if ( !empty($email) )

$host = sprintf( "http://%d.gravatar.com", ( hexdec( $email_hash\[0\] ) % 2 ) );

else

$host = 'http://0.gravatar.com';

}

　　将以上代码修改为下面的代码，问题即可解决：

if ( is_ssl() )

$host = 'https://secure.gravatar.com';

else $host = 'http://www.gravatar.com';