# harbor

## harbor地址和账号

> [地址](http://10.1.104.204:1880/)
>
> admin / 25iu5rewn9qSEcL

## 登录harbor

```bash
docker login -u admin -p 25iu5rewn9qSEcL 10.1.104.204:1880

docker build -t 10.1.104.204:1880/library/jenkins:node .
docker push 10.1.104.204:1880/library/jenkins:node
```
