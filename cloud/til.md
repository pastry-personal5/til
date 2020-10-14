## Docker Private Image Registry가 같은 클러스터에 있을 때, 접근 방법은?

* Problem: 평범한 방법으로는 Docker Private Image Registry 내 Image를 Pull 못한다.
* Problem: Node 상의 Docker는 Kubernetes 내 Internal DNS를 쓰지 못한다.

https://stackoverflow.com/questions/52675900/kubernetes-pull-images-from-internal-registry-with-on-premise-deployment

## 특정 Node 내 Docker Image Cache를 모두 지우는 방법은?

https://forums.docker.com/t/how-to-delete-cache/5753
