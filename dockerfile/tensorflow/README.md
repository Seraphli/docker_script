Dockerfile for generating tensorflow docker image. This dockerfile is based on cuda 10.1 with cudnn.

While building image, you can replace mirror url with build arguments.

For example:

```
docker build -t seraphlivery/tensorflow:1.15 --build-arg PYTHON_MIRROR=https://npm.taobao.org/mirrors/ --build-arg PYPI_MIRROR=https://pypi.tuna.tsinghua.edu.cn/simple/ .
```
