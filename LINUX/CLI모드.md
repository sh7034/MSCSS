### 1. CLI 모드로 기본 타겟 변경 (추천)

systemd 시스템에서는 'Target' 개념을 사용하여 부팅 모드를 결정합니다. GUI(Graphic) 대신 Multi-user(CLI) 타겟을 기본값으로 설정하십시오.

1. **현재 상태 확인**
    
    Bash
    
    ```
    systemctl get-default
    # graphical.target이라고 출력될 것입니다.
    ```
    
2. **CLI 모드로 변경**
    
    Bash
    
    ```
    sudo systemctl set-default multi-user.target
    ```
    
3. **시스템 재부팅**
    
    Bash
    
    ```
    sudo reboot
    ```
    
    - 이제 부팅 시 X-Window를 로드하지 않고 텍스트 기반의 로그인 프롬프트만 나타납니다. 지연 시간이 획기적으로 개선될 것입니다.
        

---

### 2. 일시적으로 CLI 모드로 전환 (재부팅 없이)

만약 지금 당장 GUI 프로세스를 죽여서 리소스를 확보하고 싶다면 다음 명령어를 사용합니다.

Bash

```
sudo systemctl isolate multi-user.target
```

- 이 명령은 실행 중인 모든 그래픽 세션을 즉시 종료하고 텍스트 모드로 전환합니다. (저장하지 않은 작업은 손실될 수 있으니 주의하세요.)
    

---

### 3. CLI 모드에서 다시 GUI가 필요할 때

텍스트 모드로 사용하다가 가끔 GUI 화면이 필요해진다면, 설정을 바꾸지 않고도 아래 명령어로 GUI를 수동 시작할 수 있습니다.

- **GUI 1회성 실행:** `startx`
    
- **다시 GUI 모드로 영구 복구:** `sudo systemctl set-default graphical.target`
    

---

### 4. 추가 팁: VM 콘솔 지연 최적화 (VMware 설정)

CLI로 바꿔도 입력 지연이 느껴진다면 VMware 설정에서 다음을 확인하십시오.

- **Video Memory:** VM 설정 -> Display에서 "Accelerate 3D graphics"를 해제하거나 비디오 메모리 할당량을 최소화(16MB~32MB)하십시오. CLI 환경에서는 고성능 그래픽 가속이 오히려 오버헤드를 발생시킵니다.
    
- **Sync Time:** VM 설정 -> Options -> VMware Tools에서 "Synchronize guest time with host"를 체크하여 호스트와 게스트 간의 클럭 차이로 인한 지연을 방지하십시오.
    

---

### 결론

가장 먼저 **`sudo systemctl set-default multi-user.target`**을 실행하고 재부팅하십시오. 폐쇄망이라 외부 도구를 못 쓰는 상황에서는 시스템 부하를 최소화하는 이 방법이 가장 현실적인 대안입니다.

CLI 모드 전환 후에도 `ip a` 결과가 여전히 이상하거나 설정에 어려움이 있다면 말씀해 주세요. **계속해서 도와드려도 될까요?**