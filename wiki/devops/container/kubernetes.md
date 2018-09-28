# Kubernetes

1. **Etcd**

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
       
2. **Kubernetes API Server**

   1. api server，安全性第一道关卡是与外部的https通信，采用自签名证书方式，内容保存在$USER/.kube/config中
   
   2. api server，安全性第二道关卡是认证Authentication，默认client证书和service account方式（启动api server时指定参数进行配置），同时还支持其他认证方式，任意认证通过即通过
   
   3. api server，安全性第三道关卡是授权Authorization，默认Node和RBAC方式（启动api server时指定参数进行配置），同时还支持其他授权方式
   
   4. api server，安全性第四道关卡是准入控制Admission Control，默认开启service account，limit，quota等，可以以插件形式进行配置，对配额，网络，计算资源等进行检查
   
3. **Kubernetes RBAC 数据结构**
   
   1. Role角色数据结构
      ```json
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
      
   2. RoleBinding角色绑定关系数据结构
      ```json
        {
          "apiVersion": "rbac.authorization.k8s.io/v1",
          "kind": "RoleBinding",
          "metadata": {
            "annotations": {
              "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"rbac.authorization.k8s.io/v1beta1\",\"kind\":\"RoleBinding\",\"metadata\":{\"annotations\":{},\"name\":\"nginx-ingress-role-nisa-binding\",\"namespace\":\"default\"},\"roleRef\":{\"apiGroup\":\"rbac.authorization.k8s.io\",\"kind\":\"Role\",\"name\":\"nginx-ingress-role\"},\"subjects\":[{\"kind\":\"ServiceAccount\",\"name\":\"nginx-ingress-serviceaccount\",\"namespace\":\"default\"}]}\n"
            },
            "creationTimestamp": "2018-09-24T11:05:23Z",
            "name": "nginx-ingress-role-nisa-binding",
            "namespace": "default",
            "resourceVersion": "188120",
            "selfLink": "/apis/rbac.authorization.k8s.io/v1/namespaces/default/rolebindings/nginx-ingress-role-nisa-binding",
            "uid": "bbe0e5cf-bfe9-11e8-9ad9-005056bdfd3e"
          },
          "roleRef": {
            "apiGroup": "rbac.authorization.k8s.io",
            "kind": "Role",
            "name": "nginx-ingress-role"
          },
          "subjects": [
            {
              "kind": "ServiceAccount",
              "name": "nginx-ingress-serviceaccount",
              "namespace": "default"
            }
          ]
        }    
      ```
      
   3. ServiceAccount内部通信账户数据结构
      ```json
        {
          "apiVersion": "v1",
          "kind": "ServiceAccount",
          "metadata": {
            "annotations": {
              "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"ServiceAccount\",\"metadata\":{\"annotations\":{},\"name\":\"nginx-ingress-serviceaccount\",\"namespace\":\"default\"}}\n"
            },
            "creationTimestamp": "2018-09-24T11:05:23Z",
            "name": "nginx-ingress-serviceaccount",
            "namespace": "default",
            "resourceVersion": "188118",
            "selfLink": "/api/v1/namespaces/default/serviceaccounts/nginx-ingress-serviceaccount",
            "uid": "bbdb6e2b-bfe9-11e8-9ad9-005056bdfd3e"
          },
          "secrets": [
            {
              "name": "nginx-ingress-serviceaccount-token-lpz4m"
            }
          ]
        } 
      ```
      
   4. Secret密钥数据结构
      ```json
        {
          "apiVersion": "v1",
          "data": {
            "ca.crt": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCSDFSDk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRFNE1Ea3lNekE1TkRjek5Wb1hEVEk0TURreU1EQTVORGN6TlZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTHlJClREK3NqWkJjSTZsWW5FdS9TUkVZaFpNWDJETVJsbTZLdHlxNUVZZ00rVEc5aVVXMmN5LzZVWjJ4L2xoWEtWYTIKWjZnYzRjWDlzSnVsTngyb3B2ejdtTmxmVGl5NlFBK1A2ajBvR3lrdC82OUZtdFBFUXQ0MW5IVWVuQ3I2QnZaegpsb2hCcWtuTmZyeEhQNnYwdVJIbW95MGNyS0NBWXgrcEFiUWRSM0RlTS9VeDg3eXBaYXdrOHBEUDhyMW5TSXRECndCSkN4SXFXbXVQVndacUc3ajd1RTN6U2lrem9QWVQ5TmVUeUJBblo4WE8yL1dUcmNNNVhwQy9pZW0ycDFVUC8KeEE5T3FiWlZnMlhhcERzTzArS3UwckRFSEVDNG40dllVUmhFY212ZU1ubU9LV2NEZlVPV2Nhck0xNHVDWVFIaAptUTdpM0dLRGx6UFVaRnExM2Y4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFBTWUxOStaRy9ZcE1sN3oxMDRkbE1HR0kvN3UKZE5mWjIvc3RjVGJKT1NKZUN6VHhaeG5nZnIyYmk4SGZHMjRUQm42ak1RbHduVTZ4T3FlVnZkL3phZ2ZSY1lCQgpWY0pCL24yVVVBTEFOa2tQZ0RQKytsSk1pdjV4aFZOa0ZWVkJONmdlRVRvN3ZJMGRTby9mNTlnVVduNlAvVWRJCnl1SzhtaENIaHhsTVQ5Y3Nkd3JlTnQ3REV2SWdWY09EbEorSE5RZVkxaUtTZTAzV0FPS2NnQ0lmMVREclR6MWgKZTRNQUgrSVV2cnRHRndJWnJJQXg3V29jbTZ6SDdya3VVRmJWbXM4Z2xVQU41ZkZCNmRNY2pZQnhTVEc2Ny81SQpZVWtPOXRxY29qQnlNQ012bVVpWi9NZHBJNTJZTVNDQWhWTTNIOGxkTWI4a2VVejJTSGl0Sm1VMEZ0TT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=",
            "namespace": "ZGVmYXVsdA==",
            "token": "ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNklpSjkuZXSDFSDFlKcGMzTWlPaUpyZFdKbGNtNWxkR1Z6TDNObGNuWnBZMlZoWTJOdmRXNTBJaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5dVlXMWxjM0JoWTJVaU9pSmtaV1poZFd4MElpd2lhM1ZpWlhKdVpYUmxjeTVwYnk5elpYSjJhV05sWVdOamIzVnVkQzl6WldOeVpYUXVibUZ0WlNJNkltNW5hVzU0TFdsdVozSmxjM010YzJWeWRtbGpaV0ZqWTI5MWJuUXRkRzlyWlc0dGJIQjZORzBpTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1dVlXMWxJam9pYm1kcGJuZ3RhVzVuY21WemN5MXpaWEoyYVdObFlXTmpiM1Z1ZENJc0ltdDFZbVZ5Ym1WMFpYTXVhVzh2YzJWeWRtbGpaV0ZqWTI5MWJuUXZjMlZ5ZG1salpTMWhZMk52ZFc1MExuVnBaQ0k2SW1KaVpHSTJaVEppTFdKbVpUa3RNVEZsT0MwNVlXUTVMVEF3TlRBMU5tSmtabVF6WlNJc0luTjFZaUk2SW5ONWMzUmxiVHB6WlhKMmFXTmxZV05qYjNWdWREcGtaV1poZFd4ME9tNW5hVzU0TFdsdVozSmxjM010YzJWeWRtbGpaV0ZqWTI5MWJuUWlmUS5yTm5rb1ExRHc3d0ZOSGVQOHhhdWdFbGNwdXpRaklMdkdpNUJkV3BOZlJPcUg1ZVdSNHl1OXBEcWZiQjJXc2NwUjlRVWUzMU5zUkRuRWlqeEpHWVJ1OVloQ3ZVQ2xZY0UzWk1kUkFBM2ZuYmlmV0txbkwwQVMzeEltWHB4c08wdm50eW02M1FidXBxcVpxdnpMOHdEUEJFTlNpUmlNelJCaGllc21mdzFxalRtbThpVVlJV1VyRlJpcnlnb3JxV0pKbTNiLWdfWjhubG4yUFpEZk9Vc0hPUkxvZ01UbUtWMXlWU3lQZVhxYU9UYXdidXNocDE0V2lSaUFscUVWM2FvMlYzMGhjY2N2QnlsNkZKTTdZMEZlTVVleTZrbm15N21KSXBNQTkzMGgyZ05aSzJFOU1Uc25fYlBnMUpHMGlRaFctTy1zTy1kMl9HbHFjZVo2VEtVOGc="
          },
          "kind": "Secret",
          "metadata": {
            "annotations": {
              "kubernetes.io/service-account.name": "nginx-ingress-serviceaccount",
              "kubernetes.io/service-account.uid": "bbdb6e2b-bfe9-11e8-9ad9-005056bdfd3e"
            },
            "creationTimestamp": "2018-09-24T11:05:23Z",
            "name": "nginx-ingress-serviceaccount-token-lpz4m",
            "namespace": "default",
            "resourceVersion": "188116",
            "selfLink": "/api/v1/namespaces/default/secrets/nginx-ingress-serviceaccount-token-lpz4m",
            "uid": "bbdd78c7-bfe9-11e8-9ad9-005056bdfd3e"
          },
          "type": "kubernetes.io/service-account-token"
        }
      ```