# Istio

1. **集成**
   ```
   基于kubernets进行集成
   kubectl label namespace default istio-injection=enabled
   ```
   
   > 在kubernetes基础上增加概念kubectl get gateway(类型为LoadBalance的ingress)

   > 在kubernetes基础上增加概念kubectl get destinationrules

   > 在kubernetes基础上增加概念kubectl get virtualservices 