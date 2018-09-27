# kubernetes

1. **etcd**

   1. kubectl查询出来的数据都保存在etcd中，同时$USER/.kube目录下存在api缓存数据
      ```bash 
      kubectl get -o json role nginx-ingress-role
   
      {
        "apiVersion": "rbac.authorization.k8s.io/v1",
        "kind": "Role",
        "metadata": {
        "annotations": {
          "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"rbac.authorization.k8s.io/v1beta1\",\"kind\":\"Role\",\"metadata\":{\"annotations\":{},\"name\":\"nginx-ingress-role\",\"namespace\":\"default\"},\"rules\":[{\"apiGroups\":[\"\"],\"resources\":[\"configmaps\",\"pods\",\"secrets\",\"namespaces\"],\"verbs\":[\"get\"]},{\"apiGroups\":[\"\"],\"resourceNames\":[\"ingress-controller-leader-nginx\"],\"resources\":[\"configmaps\"],\"verbs\":[\"get\",\"update\"]},{\"apiGroups\":[\"\"],\"resources\":[\"configmaps\"],\"verbs\":[\"create\"]},{\"apiGroups\":[\"\"],\"resources\":[\"endpoints\"],\"verbs\":[\"get\"]}]}\n"
        },
        "creationTimestamp": "2018-09-24T11:05:23Z",
        "name": "nginx-ingress-role",
        "namespace": "default",
        "resourceVersion": "188119",
        "selfLink": "/apis/rbac.authorization.k8s.io/v1/namespaces/default/roles/nginx-ingress-role",
        "uid": "bbdf6335-bfe9-11e8-9ad9-005056bdfd3e"
        },
        "rules": [
        {
          "apiGroups": [
            ""
          ],
          "resources": [
            "configmaps",
            "pods",
            "secrets",
            "namespaces"
          ],
          "verbs": [
            "get"
          ]
        },
        {
          "apiGroups": [
            ""
          ],
          "resourceNames": [
            "ingress-controller-leader-nginx"
          ],
          "resources": [
            "configmaps"
          ],
          "verbs": [
            "get",
            "update"
          ]
        },
        {
          "apiGroups": [
            ""
          ],
          "resources": [
            "configmaps"
          ],
          "verbs": [
            "create"
          ]
        },
        {
          "apiGroups": [
            ""
          ],
          "resources": [
            "endpoints"
          ],
          "verbs": [
            "get"
          ]
        }
        ]
      }
      ```
   
   2. 使用etcdctl get 命令直接查询数据库得到如下数据，json格式的数据是采用base64进行了encode，解码时需要采用utf-7编码方式进行decode
       ```bash
       ./etcdctl --endpoints=https://172.16.135.170:2379 get /registry/roles/default/nginx-ingress-role -w json
 
       {
          "header": {
            "cluster_id": 14685325193214431000,
            "member_id": 12685387513597043000,
            "revision": 710790,
            "raft_term": 51
          },
          "kvs": [
            {
              "key": "L3JlZ2lzdHJ5L3JvbGVzL2RlZmF1bHQvbmdpbngtaW5ncmVzcy1yb2xl",
              "create_revision": 188119,
              "mod_revision": 188119,
              "version": 1,
              "value": "azhzAAokChxyYmFjLmF1dGhvcml6YXRpb24uazhzLmlvL3YxEgRSb2xlEpwGCv8EChJuZ2lueC1pbmdyZXNzLXJvbGUSABoHZGVmYXVsdCIAKiRiYmRmNjMzNS1iZmU5LTExZTgtOWFkOS0wMDUwNTZiZGZkM2UyADgAQggI84mj3QUQAGKlBAowa3ViZWN0bC5rdWJlcm5ldGVzLmlvL2xhc3QtYXBwbGllZC1jb25maWd1cmF0aW9uEvADeyJhcGlWZXJzaW9uIjoicmJhYy5hdXRob3JpemF0aW9uLms4cy5pby92MWJldGExIiwia2luZCI6IlJvbGUiLCJtZXRhZGF0YSI6eyJhbm5vdGF0aW9ucyI6e30sIm5hbWUiOiJuZ2lueC1pbmdyZXNzLXJvbGUiLCJuYW1lc3BhY2UiOiJkZWZhdWx0In0sInJ1bGVzIjpbeyJhcGlHcm91cHMiOlsiIl0sInJlc291cmNlcyI6WyJjb25maWdtYXBzIiwicG9kcyIsInNlY3JldHMiLCJuYW1lc3BhY2VzIl0sInZlcmJzIjpbImdldCJdfSx7ImFwaUdyb3VwcyI6WyIiXSwicmVzb3VyY2VOYW1lcyI6WyJpbmdyZXNzLWNvbnRyb2xsZXItbGVhZGVyLW5naW54Il0sInJlc291cmNlcyI6WyJjb25maWdtYXBzIl0sInZlcmJzIjpbImdldCIsInVwZGF0ZSJdfSx7ImFwaUdyb3VwcyI6WyIiXSwicmVzb3VyY2VzIjpbImNvbmZpZ21hcHMiXSwidmVyYnMiOlsiY3JlYXRlIl19LHsiYXBpR3JvdXBzIjpbIiJdLCJyZXNvdXJjZXMiOlsiZW5kcG9pbnRzIl0sInZlcmJzIjpbImdldCJdfV19CnoAEi4KA2dldBIAGgpjb25maWdtYXBzGgRwb2RzGgdzZWNyZXRzGgpuYW1lc3BhY2VzEjwKA2dldAoGdXBkYXRlEgAaCmNvbmZpZ21hcHMiH2luZ3Jlc3MtY29udHJvbGxlci1sZWFkZXItbmdpbngSFgoGY3JlYXRlEgAaCmNvbmZpZ21hcHMSEgoDZ2V0EgAaCWVuZHBvaW50cxoAIgA="
            }
          ],
          "count": 1
       }
       ```
       
2. **kubernetes api server**

   1. api server，安全性第一道关卡是与外部的https通信，采用自签名证书方式，内容保存在$USER/.kube/config中
   
   2. api server，安全性第二道关卡是认证Authentication，默认必须开启service account方式（启动api server时指定参数进行配置），同时还支持其他认证方式，任意认证通过即通过
   
   3. api server，安全性第三道关卡是授权Authorization，默认RBAC方式（启动api server时指定参数进行配置），同时还支持其他授权方式
   
   4. api server，安全性第四道关卡是准入控制Admission Control，默认开启service account，limit，quota等，可以以插件形式进行配置，对配额，网络，计算资源等进行检查