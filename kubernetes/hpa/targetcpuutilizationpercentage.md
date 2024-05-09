# targetCPUUtilizationPercentage 계산은 어떻게 되는가

{% hint style="info" %}
targetCPUUtilizationPercentage 계산은 어떻게 되는가
{% endhint %}

* **`targetCPUUtilizationPercentage` 계산 방식**:
  * `targetCPUUtilizationPercentage`는 현재 CPU 사용량이 `requests.cpu`에 대해 차지하는 백분율을 기반으로 계산

#### 공식 문서의 공식 내용

**공식 문서 인용**: [Horizontal Pod Autoscaler Walkthrough (Kubernetes 공식 문서)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)

#### `desiredReplicas` 공식 설명

```plaintext
desiredReplicas = ceil(currentReplicas * ( currentMetricValue / desiredMetricValue ))
```

1. **currentReplicas**:
   * 현재 실행 중인 파드의 .
   * 예를 들어, 1개 파드로 시작하면 `currentReplicas`는 `1`.
2. **currentMetricValue**:
   * 현재 메트릭 값.
   * CPU 사용량을 기준으로 한다면 현재 CPU 사용량이 된다.
   * 예를 들어, 현재 CPU 사용량이 `80m`일 때 `currentMetricValue`는 `80m`.
3. **desiredMetricValue**:
   * 목표 메트릭 값.
   * CPU 사용률을 기준으로 한다면 `targetCPUUtilizationPercentage`에 해당하는 값이 된다.
   * 예를 들어, `targetCPUUtilizationPercentage`가 `75%`로 설정되었다면 `desiredMetricValue`는 `75%`.

#### 공식 계산 예시

1.  **공식**:

    ```plaintext
    desiredReplicas = ceil(currentReplicas * (currentMetricValue / desiredMetricValue))
    ```
2. **대입 값**:
   * `currentReplicas = 1`
   * `currentMetricValue = 80m`
   * `desiredMetricValue = 75%`
3. **계산 과정**:
   * **첫 번째 단계**: `80m / 75%`
     * `80m`을 `75%`로 나누면 `80 / 0.75`.
     * 이때 퍼센트(`%`)는 소수점으로 환산되므로, `75% = 0.75`.
     * 따라서 `80m / 75% = 80 / 0.75 = 106.67`
   * **두 번째 단계**: `ceil(1 * 106.67)`
     * `currentReplicas`가 `1`이므로 `1 * 106.67`은 `106.67`.
     * `ceil` 함수를 사용해 이 값을 올림 처리.
     * `ceil(106.67) = 2`
4. **최종 결과**:
   * 최종적으로 필요한 파드 수는 `2`개입니다.

* `ceil` 함수를 사용해 결과를 올림 처리하여 최종적으로 2개의 파드를 요구한다.
* `ceil`은 수학 함수로, 특정 숫자를 올림하여 정수로 반환하는 함수이다. 이 함수를 사용하여 HPA의 파드 수를 계산할 때 최종적으로 필요한 파드 수를 정수로 결정한다.\

* ceil 함수 설명

1. **`ceil`** 함수는 주어진 소수점 숫자를 **올림**하여 정수로 반환하는 수학 함수. 이는 특정 수가 소수점 이하 값을 가질 때, 그 수보다 크거나 같은 가장 작은 정수를 반환한다.
   1. 예를 들어, `ceil(1.0667)`은 `2`를 반환한다.
   2. `1.0667`은 소수점 이하 값이 존재하므로, 올림 처리를 위해 가장 작은 정수를 찾는다.
   3. `1.0667`보다 크거나 같은 가장 작은 정수는 **`2`**.
2. **ceil 함수의 역할**:
   * HPA에서 파드 수를 계산할 때 소수점이 발생할 수 있으므로 이를 올림하여 최종 파드 수를 정수로 결정한다.

#### 예시

```plaintext
ceil(1.0667) = 2
ceil(2.5) = 3
ceil(3.9) = 4
ceil(-1.1) = -1
```



{% hint style="info" %}
그래서 resource.request.cpu가 없으면 hpa resource가 ns에 배포되지 않는 것 같다
{% endhint %}



#### currentMetricValue와 requests의 관계

* `currentMetricValue`는 `requests`와 직접적인 관계를 갖지는 않지만, 현재 CPU 사용량을 `requests` 값에 대한 백분율로 비교해 계산된다.

#### HPA 리소스가 생성되지 않는 이유

* HPA는 **CPU 또는 메모리 requests**가 지정되지 않은 경우 작동하지 않을 수 있다.
* HPA는 `resources.requests.cpu`나 `resources.requests.memory`를 기준으로 계산하기 때문이다.
* 만약 `resources` 섹션이 비어있거나 설정되어 있지 않다면 HPA는 `currentMetricValue`를 계산할 수 없어 리소스가 생성되지 않는다.



* 공식 문서의 관련 내용은 다음과 같다:

{% embed url="https://discuss.kubernetes.io/t/kubernetes-amount-of-pods-vs-amount-of-cpu-requests/17493/2" %}

> The Horizontal Pod Autoscaler automatically scales the number of pods in a replication controller, deployment or replica set based on observed CPU utilization (or, with custom metrics support, on some other application-provided metrics).

* **CPU 또는 메모리 `requests`의 필요성**:
  * HPA가 제대로 작동하려면 `requests.cpu` 또는 `requests.memory`를 설정해야 한다.
  * `requests` 값이 없으면 HPA는 리소스 사용률을 계산할 수 없다.