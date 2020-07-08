# Jenkins的kubernetes-plugin使用方法

## 准备工作

* [安装 Jenkins](Install-Jenkins.md)
* [kubernetes-plugin的GitHub仓库](https://github.com/jenkinsci/kubernetes-plugin)
* 在Jenkins服务器上安装kubernetes、Docker、SSH Agent三个插件。

## 制作Jenkins连接kubernetes的证书

因为Jenkins服务器在kubernetes集群之外，所以我们准备以下文件才能从外面连接到kubernetes集群。

```shell
#在Master节点上执行
cat /root/.kube/config
```

内容如下：

```conf
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXlOREV6TkRrd04xb1hEVE13TURVeU1qRXpORGt3TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTlRBCnlYV1o0L1RrK0xUaTZEbmtLc0djNXhveWRvaStSWDdPWFc3VFFyTmlacVJ4TW1nYXFlaFYrKzFRY05JanZZcEsKR0JVU0E1T2JpVWVuSHdkYkNFNkMreUJUSU9NbGxkS2xiWVJaZ0tQY0prMFhKcFFyNkJWV3ViMzVKUnU0UTZLNQpDVTFrR0E3QjFBazY1V0VpcGR3d2NoekxOOGEzSGlzdmN1U2lGQ1dIL3pDQlZ3Tkl3TFY3YW9ySFp1MlBTcXl3CkJyRlNWZHk0dE1EQjhwcUllSnkvaGhlVkpaalZnU2xCS1pwMEp5bzhKZkRNZTFRcnJXWFpCS0duNElkcjdobUUKajYxZUZrQmI1M21tMTJvZnJEWDhKSHBOQ1hvVlJoTFZXalVrYVF6UTZmMnIyUWwxWkM3R2hLbXByQ25EVlpsMwpLNjlLenVTbEF4N2hhYjNnWHgwQ0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFLcFlzdXRKMFNuUkpZRHdqOUtlV0JTNTlRR0gKNENqZWJRMkROSEZGRWRYNy9iVHdabW9NeGE5VjRBaStpdFRBY21CNHdjQmhLb1gvd3lPYVphR3VRRUo2RjN3bQpEUHJJcjRiYkxWU0FCL2lhR0JnY0poeXo2WVlqaHZ2YUFsUUY0NkpvNUpQWlcrY0VEVWV6WEIrcTcrTGpNS1hPCjNiY0VzN0lXWVE3VDNGcjVtZVk1RFdydkZKYk5sdEJXcjhuQUJISVZqaFFZR09YNTVrT0dFUk51Z1A3d0dSeTAKZEpFMDRVdDYwV3pYZ3o1TEh6NU4ranJ5K2xIWjF6bVVkRXdkZGl2T2RsS1N1d0dZQUsvZnJQV3Vra0NUZTI0Vwo1VEYrM2lKYUx5dVZzTkhMNXJ5akF2K1ZIWlJrc3pHd0cwZldCMVA0RVdEak1FRkJLdlU4QnBpMTJicz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://192.168.122.3:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM4akNDQWRxZ0F3SUJBZ0lJT2NDUk4zQ2phQlF3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1qUXhNelE1TURkYUZ3MHlNVEExTWpReE16UTVNVEJhTURReApGekFWQmdOVkJBb1REbk41YzNSbGJUcHRZWE4wWlhKek1Sa3dGd1lEVlFRREV4QnJkV0psY201bGRHVnpMV0ZrCmJXbHVNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDQVFFQTI1QTlqWGlUd2UreUhrWjcKcGVGbWg5b2MrZzF5N0h6TmNuemNEZFNHeGUvcEhFekh2dWk5cmkzaGoyQmgvVmVhRTZoRnVrR3QvbS9qOExiTApFbDBFVzZhOWFDVlhnRjBnajhmQ1FOUzVGcGlZWHVJT1o3dHFjRUNXcWpMUHIrSngraDRYVU5sVE8yd2V2TmhYCnl6TTVxcVpUeGZ0TnNicGpEdVIzMU9MUm5zU2JmUHphSDdpYzJad2NJcDF3dWtxZUdNSExrelRDcGVFc1VETzIKR1FKbVpucFdTa2xyeEpvZ0syQmNqMmxBeHYwais5M3lLTHJEbDY5YTMzT1FiaG5HUTdrbDUyZE0zRXhiZkx4TgpyaDNTQnc1NVZMRWJLR2lKaVdhcnI4MDh5bzBIOSt5SU5mWXREUTlwS0NFd0M2QUROZG8xTllhR0poajNadWJICkpKdWhzd0lEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEVlIwbEJBd3dDZ1lJS3dZQkJRVUgKQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFGNmthMzZZd3F0ZkhpYXEyay9MK3piMGl2VUxoYkVwVzVCVgpoZXAxamx6eGVHRXpsckZZWWJqaU9zZU1DY2xmVmtiQWtIdGE4V1VrdElJU3lnRFJzNEF2ZHVxZDFCclppSEdoCktvdldaZ0NvWVFUeFFEOEZtRXQyMnBaaENsdnRKNXgwUWlFVjh6Njd0clZ0ak5ITStaaVRGM3QvendZRGtUWEYKaHZTTzFSRTI1Yno2a2V6NDFsS3JzaDRqSDJ5WVdnd0ltZmVreld2akVUaDI0WXhOTDcrTGhnaGtkSjRTdTNLcwpnWkIrUVRYdkcvZThlOU84OGtjZzY1ZWpPSlFsMWtwVmkrZ1B0cFZ4aUFDLzJqbmVtWlM4ZmhQM3RaejkyNGEvCitSY05Ia3RpcXJ2dXBtSzMxbS9iZXBVQ1VKU256Y29CclcvTXRmSHZkMHpyd1JmRTdVZz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBMjVBOWpYaVR3ZSt5SGtaN3BlRm1oOW9jK2cxeTdIek5jbnpjRGRTR3hlL3BIRXpICnZ1aTlyaTNoajJCaC9WZWFFNmhGdWtHdC9tL2o4TGJMRWwwRVc2YTlhQ1ZYZ0YwZ2o4ZkNRTlM1RnBpWVh1SU8KWjd0cWNFQ1dxakxQcitKeCtoNFhVTmxUTzJ3ZXZOaFh5ek01cXFaVHhmdE5zYnBqRHVSMzFPTFJuc1NiZlB6YQpIN2ljMlp3Y0lwMXd1a3FlR01ITGt6VENwZUVzVURPMkdRSm1abnBXU2tscnhKb2dLMkJjajJsQXh2MGorOTN5CktMckRsNjlhMzNPUWJobkdRN2tsNTJkTTNFeGJmTHhOcmgzU0J3NTVWTEViS0dpSmlXYXJyODA4eW8wSDkreUkKTmZZdERROXBLQ0V3QzZBRE5kbzFOWWFHSmhqM1p1YkhKSnVoc3dJREFRQUJBb0lCQUdoY0FNV3pZQmU0QkdOYgpnUDN4U3JpYnRWWXMra2pwY1FWUHo1QkxTZHZmRWwrL0p0blk2ZjlXRTNZQWk5TE14cEU5VkJjWmpNZjVDdENwCk1aQ0M0Y1ZtVlhTZyt4d3FKNFh2bnFjTjlRT0dlRFh4SUlPeGt2L1QwMGtHYWF6aDV1YkpVcSs4L0ZDc0xWZjUKUkpGeXdhQVkrb3lKeS80RDUzMTJublp6Y01zVEtxd3kyLy8zN3RFOVdra1ExVnlNRWpOcnV1OGtJWlh1UkdjTwp4ZkV2TFFZRXJMazE0alVoL0dydFJ5SVMrQVhieFFObHFIZmNlY2pwaDVjVXpldWZlWTBRK2tIdW1oWTVyN1R3CjhkaksxZFZleEZEUklKWS9ZdklIN3N0L1dZSWR1S25nZU1kaDI4UDhvY2lCNkF0VkMxQzVHcDFLdXZOOTNoTysKaVRQb1laRUNnWUVBN0xDeFRQN1RsbkNpbjUxQUNrYmh5Wkg4UVRZdS9oaHFvYVVlem4ydnFjUlZlTEVNTDN5SwpySjVxZTFlSGYwZXJNRTNWc0N0R3dTbXYxRzIzanlSYU54b3BpMUovdklxQVJEYUJYMlFma2J3YmRCSi94VGhjCmxVNHNYbWhLbmdSdFNPT2hCbUNhRXk4SlkyOHRESG1WTVZXSmlHUE8zRFBZeGVlWWJKcTBPTlVDZ1lFQTdYbmEKWGdPdjloUXhkUGtlWE1JaEIyc1h6WC9zMDlhT3pLSkhSbENUcU9JWjdkOHNBOE9jMTdsa0hwa04zdkhtdnNiNgpQUXZ0RVdSYkFkUXJnSzlFQXhvR0ptN2xaTnEzU0dHNUtFdkRRVXN3MzhEd25QK0w3NTRSK2VKOVhtRTFxcHBDCnBIZHZrYWlSaFlwZitaVExiMXR3TUlHSFZwZ3dHdit2OGtjWHRHY0NnWUE3Y1VpbDZKMmsrNWFyOVdjZTdaeEkKN2tQbThMK3VpdDFFeVY1S1I2QjFSMXliMmdNUWJmdTV6SE03bE1qdnUwVVA5WXhFR1NwRTJyNVE0RmtvTnQzOQpWckcyVHJyTDFCRFFrTXUrOUR0RmMzT0ZuQnZBb1I0SFZVR3BXQkhkUzRsU3MvQTkxTWI5S0dQNE4vU2RYRytPCm0wZldIQkxZbEtETld4T1p1dVNtSFFLQmdHZFVNZlBlWHQ0UUEwVUdjSE9qOUlNcDBmMlV3Qk9jdEIzT0xuMGwKNmxuOXA1S2RIS1cybzdRcDhxcHRwbDl2bWh0QXNQbUhDdXdhWW83MUpzaWplbEJ2TG1YaXN1V2pWVVNsWU05cgp4OVhOUmNMV1ROdkRSYnhKWjBEM2o5MVZKdWpJbmw2NXFVNFRydDRRUzVwcFordFlPZ1NoRjZaU0tmeFZiTlZhCm9iSFpBb0dBSUVETGR5elU1Sk5Pc1R3K09hWXJWWHg3K1YxUlJ5VC9ERHY4ems3K0c2dmxrOXVrQ0w3eEhoV20Ka3k0QTN2cFNHV2x0L0JpeDJPWlFUek1hQmR0bmZkNURaZHdua2FNTmg4dXNFSjNJWE5DVnFhK3JWZlg4Z0orNQpQckRXdFNyQkdjYlh4ZEdLcWFvNHBuVjc2dmhSSzRJU3QwUEoycXMrMU4vWG1RdmVod2M9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==
```

制作登录K8S集群的证书

```shell
#将certificate-authority-data的内容复制出来在本地制作kube-ca.crt
echo LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXlOREV6TkRrd04xb1hEVE13TURVeU1qRXpORGt3TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTlRBCnlYV1o0L1RrK0xUaTZEbmtLc0djNXhveWRvaStSWDdPWFc3VFFyTmlacVJ4TW1nYXFlaFYrKzFRY05JanZZcEsKR0JVU0E1T2JpVWVuSHdkYkNFNkMreUJUSU9NbGxkS2xiWVJaZ0tQY0prMFhKcFFyNkJWV3ViMzVKUnU0UTZLNQpDVTFrR0E3QjFBazY1V0VpcGR3d2NoekxOOGEzSGlzdmN1U2lGQ1dIL3pDQlZ3Tkl3TFY3YW9ySFp1MlBTcXl3CkJyRlNWZHk0dE1EQjhwcUllSnkvaGhlVkpaalZnU2xCS1pwMEp5bzhKZkRNZTFRcnJXWFpCS0duNElkcjdobUUKajYxZUZrQmI1M21tMTJvZnJEWDhKSHBOQ1hvVlJoTFZXalVrYVF6UTZmMnIyUWwxWkM3R2hLbXByQ25EVlpsMwpLNjlLenVTbEF4N2hhYjNnWHgwQ0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFLcFlzdXRKMFNuUkpZRHdqOUtlV0JTNTlRR0gKNENqZWJRMkROSEZGRWRYNy9iVHdabW9NeGE5VjRBaStpdFRBY21CNHdjQmhLb1gvd3lPYVphR3VRRUo2RjN3bQpEUHJJcjRiYkxWU0FCL2lhR0JnY0poeXo2WVlqaHZ2YUFsUUY0NkpvNUpQWlcrY0VEVWV6WEIrcTcrTGpNS1hPCjNiY0VzN0lXWVE3VDNGcjVtZVk1RFdydkZKYk5sdEJXcjhuQUJISVZqaFFZR09YNTVrT0dFUk51Z1A3d0dSeTAKZEpFMDRVdDYwV3pYZ3o1TEh6NU4ranJ5K2xIWjF6bVVkRXdkZGl2T2RsS1N1d0dZQUsvZnJQV3Vra0NUZTI0Vwo1VEYrM2lKYUx5dVZzTkhMNXJ5akF2K1ZIWlJrc3pHd0cwZldCMVA0RVdEak1FRkJLdlU4QnBpMTJicz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo= | base64 -d > ~/kube-cert/kube-ca.crt

#将client-certificate-data的内容复制出来在本地制作kube-client.crt
echo LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM4akNDQWRxZ0F3SUJBZ0lJT2NDUk4zQ2phQlF3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1qUXhNelE1TURkYUZ3MHlNVEExTWpReE16UTVNVEJhTURReApGekFWQmdOVkJBb1REbk41YzNSbGJUcHRZWE4wWlhKek1Sa3dGd1lEVlFRREV4QnJkV0psY201bGRHVnpMV0ZrCmJXbHVNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDQVFFQTI1QTlqWGlUd2UreUhrWjcKcGVGbWg5b2MrZzF5N0h6TmNuemNEZFNHeGUvcEhFekh2dWk5cmkzaGoyQmgvVmVhRTZoRnVrR3QvbS9qOExiTApFbDBFVzZhOWFDVlhnRjBnajhmQ1FOUzVGcGlZWHVJT1o3dHFjRUNXcWpMUHIrSngraDRYVU5sVE8yd2V2TmhYCnl6TTVxcVpUeGZ0TnNicGpEdVIzMU9MUm5zU2JmUHphSDdpYzJad2NJcDF3dWtxZUdNSExrelRDcGVFc1VETzIKR1FKbVpucFdTa2xyeEpvZ0syQmNqMmxBeHYwais5M3lLTHJEbDY5YTMzT1FiaG5HUTdrbDUyZE0zRXhiZkx4TgpyaDNTQnc1NVZMRWJLR2lKaVdhcnI4MDh5bzBIOSt5SU5mWXREUTlwS0NFd0M2QUROZG8xTllhR0poajNadWJICkpKdWhzd0lEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEVlIwbEJBd3dDZ1lJS3dZQkJRVUgKQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFGNmthMzZZd3F0ZkhpYXEyay9MK3piMGl2VUxoYkVwVzVCVgpoZXAxamx6eGVHRXpsckZZWWJqaU9zZU1DY2xmVmtiQWtIdGE4V1VrdElJU3lnRFJzNEF2ZHVxZDFCclppSEdoCktvdldaZ0NvWVFUeFFEOEZtRXQyMnBaaENsdnRKNXgwUWlFVjh6Njd0clZ0ak5ITStaaVRGM3QvendZRGtUWEYKaHZTTzFSRTI1Yno2a2V6NDFsS3JzaDRqSDJ5WVdnd0ltZmVreld2akVUaDI0WXhOTDcrTGhnaGtkSjRTdTNLcwpnWkIrUVRYdkcvZThlOU84OGtjZzY1ZWpPSlFsMWtwVmkrZ1B0cFZ4aUFDLzJqbmVtWlM4ZmhQM3RaejkyNGEvCitSY05Ia3RpcXJ2dXBtSzMxbS9iZXBVQ1VKU256Y29CclcvTXRmSHZkMHpyd1JmRTdVZz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo= | base64 -d > ~/kube-cert/kube-client.crt

#将client-key-data的内容复制出来在本地制作kube-client.key
echo LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBMjVBOWpYaVR3ZSt5SGtaN3BlRm1oOW9jK2cxeTdIek5jbnpjRGRTR3hlL3BIRXpICnZ1aTlyaTNoajJCaC9WZWFFNmhGdWtHdC9tL2o4TGJMRWwwRVc2YTlhQ1ZYZ0YwZ2o4ZkNRTlM1RnBpWVh1SU8KWjd0cWNFQ1dxakxQcitKeCtoNFhVTmxUTzJ3ZXZOaFh5ek01cXFaVHhmdE5zYnBqRHVSMzFPTFJuc1NiZlB6YQpIN2ljMlp3Y0lwMXd1a3FlR01ITGt6VENwZUVzVURPMkdRSm1abnBXU2tscnhKb2dLMkJjajJsQXh2MGorOTN5CktMckRsNjlhMzNPUWJobkdRN2tsNTJkTTNFeGJmTHhOcmgzU0J3NTVWTEViS0dpSmlXYXJyODA4eW8wSDkreUkKTmZZdERROXBLQ0V3QzZBRE5kbzFOWWFHSmhqM1p1YkhKSnVoc3dJREFRQUJBb0lCQUdoY0FNV3pZQmU0QkdOYgpnUDN4U3JpYnRWWXMra2pwY1FWUHo1QkxTZHZmRWwrL0p0blk2ZjlXRTNZQWk5TE14cEU5VkJjWmpNZjVDdENwCk1aQ0M0Y1ZtVlhTZyt4d3FKNFh2bnFjTjlRT0dlRFh4SUlPeGt2L1QwMGtHYWF6aDV1YkpVcSs4L0ZDc0xWZjUKUkpGeXdhQVkrb3lKeS80RDUzMTJublp6Y01zVEtxd3kyLy8zN3RFOVdra1ExVnlNRWpOcnV1OGtJWlh1UkdjTwp4ZkV2TFFZRXJMazE0alVoL0dydFJ5SVMrQVhieFFObHFIZmNlY2pwaDVjVXpldWZlWTBRK2tIdW1oWTVyN1R3CjhkaksxZFZleEZEUklKWS9ZdklIN3N0L1dZSWR1S25nZU1kaDI4UDhvY2lCNkF0VkMxQzVHcDFLdXZOOTNoTysKaVRQb1laRUNnWUVBN0xDeFRQN1RsbkNpbjUxQUNrYmh5Wkg4UVRZdS9oaHFvYVVlem4ydnFjUlZlTEVNTDN5SwpySjVxZTFlSGYwZXJNRTNWc0N0R3dTbXYxRzIzanlSYU54b3BpMUovdklxQVJEYUJYMlFma2J3YmRCSi94VGhjCmxVNHNYbWhLbmdSdFNPT2hCbUNhRXk4SlkyOHRESG1WTVZXSmlHUE8zRFBZeGVlWWJKcTBPTlVDZ1lFQTdYbmEKWGdPdjloUXhkUGtlWE1JaEIyc1h6WC9zMDlhT3pLSkhSbENUcU9JWjdkOHNBOE9jMTdsa0hwa04zdkhtdnNiNgpQUXZ0RVdSYkFkUXJnSzlFQXhvR0ptN2xaTnEzU0dHNUtFdkRRVXN3MzhEd25QK0w3NTRSK2VKOVhtRTFxcHBDCnBIZHZrYWlSaFlwZitaVExiMXR3TUlHSFZwZ3dHdit2OGtjWHRHY0NnWUE3Y1VpbDZKMmsrNWFyOVdjZTdaeEkKN2tQbThMK3VpdDFFeVY1S1I2QjFSMXliMmdNUWJmdTV6SE03bE1qdnUwVVA5WXhFR1NwRTJyNVE0RmtvTnQzOQpWckcyVHJyTDFCRFFrTXUrOUR0RmMzT0ZuQnZBb1I0SFZVR3BXQkhkUzRsU3MvQTkxTWI5S0dQNE4vU2RYRytPCm0wZldIQkxZbEtETld4T1p1dVNtSFFLQmdHZFVNZlBlWHQ0UUEwVUdjSE9qOUlNcDBmMlV3Qk9jdEIzT0xuMGwKNmxuOXA1S2RIS1cybzdRcDhxcHRwbDl2bWh0QXNQbUhDdXdhWW83MUpzaWplbEJ2TG1YaXN1V2pWVVNsWU05cgp4OVhOUmNMV1ROdkRSYnhKWjBEM2o5MVZKdWpJbmw2NXFVNFRydDRRUzVwcFordFlPZ1NoRjZaU0tmeFZiTlZhCm9iSFpBb0dBSUVETGR5elU1Sk5Pc1R3K09hWXJWWHg3K1YxUlJ5VC9ERHY4ems3K0c2dmxrOXVrQ0w3eEhoV20Ka3k0QTN2cFNHV2x0L0JpeDJPWlFUek1hQmR0bmZkNURaZHdua2FNTmg4dXNFSjNJWE5DVnFhK3JWZlg4Z0orNQpQckRXdFNyQkdjYlh4ZEdLcWFvNHBuVjc2dmhSSzRJU3QwUEoycXMrMU4vWG1RdmVod2M9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg== | base64 -d > ~/kube-cert/kube-client.key

#创建证书，创建证书需要设置密码，设置的密码不要忘记
openssl pkcs12 -export -out ~/kube-cert/kube-cert.pfx -inkey ~/kube-cert/kube-client.key -in ~/kube-cert/kube-client.crt -certfile ~/kube-cert/kube-ca.crt
```

此时我们创建了4个文件：kube-ca.crt、kube-cert.pfx、kube-client.crt、kube-client.key，其中kube-ca.crt的文件内容在创建Jenkins连接到kubernetes集群时需要用，kube-cert.pfx需要上传到Jenkins的凭据配置中：

```shell
cat kube-ca.crt
```

```txt
-----BEGIN CERTIFICATE-----
MIICyDCCAbCgAwIBAgIBADANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwprdWJl
cm5ldGVzMB4XDTIwMDUyNDEzNDkwN1oXDTMwMDUyMjEzNDkwN1owFTETMBEGA1UE
AxMKa3ViZXJuZXRlczCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBANTA
yXWZ4/Tk+LTi6DnkKsGc5xoydoi+RX7OXW7TQrNiZqRxMmgaqehV++1QcNIjvYpK
GBUSA5ObiUenHwdbCE6C+yBTIOMlldKlbYRZgKPcJk0XJpQr6BVWub35JRu4Q6K5
CU1kGA7B1Ak65WEipdwwchzLN8a3HisvcuSiFCWH/zCBVwNIwLV7aorHZu2PSqyw
BrFSVdy4tMDB8pqIeJy/hheVJZjVgSlBKZp0Jyo8JfDMe1QrrWXZBKGn4Idr7hmE
j61eFkBb53mm12ofrDX8JHpNCXoVRhLVWjUkaQzQ6f2r2Ql1ZC7GhKmprCnDVZl3
K69KzuSlAx7hab3gXx0CAwEAAaMjMCEwDgYDVR0PAQH/BAQDAgKkMA8GA1UdEwEB
/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEBAKpYsutJ0SnRJYDwj9KeWBS59QGH
4CjebQ2DNHFFEdX7/bTwZmoMxa9V4Ai+itTAcmB4wcBhKoX/wyOaZaGuQEJ6F3wm
DPrIr4bbLVSAB/iaGBgcJhyz6YYjhvvaAlQF46Jo5JPZW+cEDUezXB+q7+LjMKXO
3bcEs7IWYQ7T3Fr5meY5DWrvFJbNltBWr8nABHIVjhQYGOX55kOGERNugP7wGRy0
dJE04Ut60WzXgz5LHz5N+jry+lHZ1zmUdEwddivOdlKSuwGYAK/frPWukkCTe24W
5TF+3iJaLyuVsNHL5ryjAv+VHZRkszGwG0fWB1P4EWDjMEFBKvU8Bpi12bs=
-----END CERTIFICATE-----
```

## Jenkins连接到kubernetes集群

打开Jenkins管理后台，选择左侧凭据，选择打开界面中的全局，点击左侧添加凭据菜单，在右侧打开界面中类型选择certificate，范围全局，证书选择Upload PKCS#12 certificate,下方密码框选择Change Password后输入创建证书时设置的密码
，下方描述填写为k8s，最后点击确定。

![Alt text](http://static.bluersw.com/images/Jenkins/j-k-01.png)
![Alt text](http://static.bluersw.com/images/Jenkins/j-k-02.png)

回到Jenkins管理后台，选择左侧系统管理，选择右侧打开界面的系统配置，在最下方打开Cloud配置界面，在配置界面中点击add a new cloud选择kubernetes后，打开详细配置信息页：

* Kubernetes 地址：kubernetes服务地址，也就是apiserver的地址，一般是master节点NodeIP+6443端口，证书里会含合法的地址列表，比如10.1.0.1, 192.168.122.3, centos7-k8s-master, kubernetes, kubernetes.default, kubernetes.default.svc, kubernetes.default.svc.cluster.local，我的Jenkins服务器和kubernetes集群不在一个网段，所以我在Host文件加入了192.168.0.10 centos7-k8s-master，然后在192.168.0.10里做了端口映射ssh -CfNg -L 6443:192.168.122.3:6443 root@127.0.0.1，才能验证通过。
* Kubernetes 服务证书 key：kube-ca.crt文件的内容。
* 凭据：刚才创建的certificate凭据。
* Jenkins 地址：Agent连接Jenkins Master的地址，比如http://ops.bluersw.com:8080 注意：Jenkins-Slave运行在Pod里，使用DNS服务解析域名，修改宿主机HOST文件不管用的（还好我有DNS服务器）。

其他都使用默认配置，点击连接测试，连接测试成功，点击Save存储。
![Alt text](http://static.bluersw.com/images/Jenkins/j-k-03.png)
图中有个警告，返回系统管理界面，选择全局安全配置，将TCP port for inbound agents修改为随机选取，点击保存，警告就消失了。

![Alt text](http://static.bluersw.com/images/Jenkins/j-k-04.png)

## 创建流水线测试任务

![Alt text](http://static.bluersw.com/images/Jenkins/j-k-06.png)

在最下方Pipeline script中填写以下脚本：

```groovy
podTemplate {
    node(POD_LABEL) {
        stage('Run shell') {
            sh 'echo hello world'
        }
    }
}
```

保存运行，查看结果，Jenkins Console Output：

```txt
Running in Durability level: MAX_SURVIVABILITY
[Pipeline] Start of Pipeline
[Pipeline] podTemplate
[Pipeline] {
[Pipeline] node
Created Pod: default/jenkins-test-6-hl7zm-w0x58-845rm
[Normal][default/jenkins-test-6-hl7zm-w0x58-845rm][Scheduled] Successfully assigned default/jenkins-test-6-hl7zm-w0x58-845rm to centos7-k8s-node1
[Normal][default/jenkins-test-6-hl7zm-w0x58-845rm][Pulled] Container image "jenkins/jnlp-slave:4.0.1-1" already present on machine
[Normal][default/jenkins-test-6-hl7zm-w0x58-845rm][Created] Created container jnlp
[Normal][default/jenkins-test-6-hl7zm-w0x58-845rm][Started] Started container jnlp
Still waiting to schedule task
‘jenkins-test-6-hl7zm-w0x58-845rm’ is offline
Agent jenkins-test-6-hl7zm-w0x58-845rm is provisioned from template Jenkins-Test_6-hl7zm-w0x58
---
apiVersion: "v1"
kind: "Pod"
metadata:
  annotations:
    buildUrl: "http://ops.bluersw.com:8080/job/Jenkins-Test/6/"
    runUrl: "job/Jenkins-Test/6/"
  labels:
    jenkins: "slave"
    jenkins/label: "Jenkins-Test_6-hl7zm"
  name: "jenkins-test-6-hl7zm-w0x58-845rm"
spec:
  containers:
  - env:
    - name: "JENKINS_SECRET"
      value: "********"
    - name: "JENKINS_AGENT_NAME"
      value: "jenkins-test-6-hl7zm-w0x58-845rm"
    - name: "JENKINS_NAME"
      value: "jenkins-test-6-hl7zm-w0x58-845rm"
    - name: "JENKINS_AGENT_WORKDIR"
      value: "/home/jenkins/agent"
    - name: "JENKINS_URL"
      value: "http://ops.bluersw.com:8080/"
    image: "jenkins/jnlp-slave:4.0.1-1"
    name: "jnlp"
    resources:
      requests:
        cpu: "100m"
        memory: "256Mi"
    volumeMounts:
    - mountPath: "/home/jenkins/agent"
      name: "workspace-volume"
      readOnly: false
  nodeSelector:
    beta.kubernetes.io/os: "linux"
  restartPolicy: "Never"
  securityContext: {}
  volumes:
  - emptyDir:
      medium: ""
    name: "workspace-volume"

Running on jenkins-test-6-hl7zm-w0x58-845rm in /home/jenkins/agent/workspace/Jenkins-Test
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Run shell)
[Pipeline] sh
+ echo hello world
hello world
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] }
[Pipeline] // podTemplate
[Pipeline] End of Pipeline
Finished: SUCCESS
```

这个名为“jenkins-test-6-hl7zm-w0x58-845rm”的Pod自动由Jenkins创建，并在执行完构建脚本之后就会自动销毁，Pod内名为“jnlp”的容器是默认必须存在的，这是执行Agent程序与Jenkins Master连接，获取并执行脚本的默认容器，这个Pod内除了jnlp容器之外可以定义其他多个容器，并指定那个容器执行什么脚本，以上是最简单的例子。