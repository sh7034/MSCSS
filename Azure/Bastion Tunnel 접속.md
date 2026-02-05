##### 변수 선언, VMID 알아내기
```
RG="st604-rg-LB-bastion"
BASTION_NAME="vnet-lab-bastion"
VM_PREFIX="rhel9-web"
VMID=$(az vm show -g $RG -n ${VM_PREFIX}-1 --query id -o tsv)
```

##### Tunnel 열기
```
az network bastion tunnel \
  -g $RG \
  -n $BASTION_NAME \
  --target-resource-id "$VMID" \
  --resource-port 22 \
  --port 2221
```

Tunnel 실행 중 메시지:
```
Opening tunnel on port: 2221
Tunnel is ready, connect on port 2221
Ctrl + C to close
```
##### Tunnel 실행 중인 호스트 컴퓨터의 새 터미널 창에서 SSH 접속
```
ssh -p 2221 -i ~/.ssh/id_ed25519 azureuser@127.0.0.1
```
