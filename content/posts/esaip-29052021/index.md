---
title: "CTF ESAIP HACK CHALLENGE 4th edition - 29 Mai 2021 Final Writeup Box Pentest Corovid"
date: 2021-06-03T13:58:26+0000
tags: ["CTF", "France", "Box", "ESAIP"]
cover: "posts/xf86-video-intel/cover.jpg"
authors: ["kn"]
description: "ESAPI 20/5/2021 writeup"
showFullContent: false
---


## Motivation

Vào một buổi trưa nắng nóng khi dịch bệnh tràn lan, mình có nhận được tin nhắn thử "xoạc" 1 cuộc thi [CTF](https://ctf.esaip.org/) do ESAIP École d'Ingénieurs tổ chức. Đây là kì thi CTF đầu tiên cũng như lần đầu tiên mình viết write up nên cũng chỉ hi vọng lót đường. 

Ở đây thì có một bài về Box pentest rất thú vị là Corovid (nó ngốn của mình 3 tiếng mới xong :( ) 

Challage web được xây dựng bằng Python ở back-end qua command `nmap -sC -sV -A -p- <ip>`. Các request được gửi từ phía Front-End đến Back-End theo các khoảng thời gian đều đặn bằng lệnh shell để để lấy kết quả mới nhất về số ca Covid trên toàn thế giới. Các request cũng đã được ký ở phía Front-End.

![Nmap scan](nmap.png)

Request Sample:

```
GET /get-last-results?
shell=cat%20/tmp/.corovid_value.txt&auth_ts=1620764393&auth_sign=d643a98d4
ce0b407beacdf750a55cff8b371d207bfbec0ae0667c1aa48b2f33a HTTP/1.1
```

![Client code](scr1.png)
![Client code](scr2.png)

Đối với những bài có sourcecode sẵn như thế này, thì mình thường lướt sơ và hiểu toàn bộ cấu trúc của source trước khi làm. Sau khi xem toàn bộ source thì mình phát hiện một số điều đáng nghi.

Code khá là lộn xộn nhưng có thể dễ dàng thấy được function y() dùng để tạo 1 request tới back-end với mẫu như ở trên. Hơn nữa, bằng cách đặt một breakpoint tại lúc tạo chữ kí với `HMAC256` thì chúng ta có thể thấy được request sẽ kèm 1 shell command tới sever để query số ca nhiễm covid ( ở đây là `cat /tmp/.corovid_value.txt`)