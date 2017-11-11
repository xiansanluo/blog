---
date: 2017-10-19T12:07:49+08:00
title: "readme"
---

# README

标签： readme

---
**目录：**

* handler
处理业务逻辑
* docker
用于构建镜像

**文件：**

* main.go
主要完成：

>配置初始化

>接入prometheus

>平滑关闭

>侦听业务端口(8080)和prometheus端口(23333)

* glide.yml
用于包管理
* Makefile
用户go test, go get等
* .gitlab-ci.yml
配置ci pipline，会使用到Makefile中的默认make（进行go test，glide install和其他依赖包的获取）

**db使用**
service.go:
```go
type stringService struct{
db *sql.DB
}

func NewService() Service {
	return &stringService{
	db: db,
	}
}
```
main.go：
```go
svc := handler.NewService(db)
```