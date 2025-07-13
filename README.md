def can_achieve(target, N, R, K, cameras):
    total_added = 0
    shield = [0] * N
    prefix = [0] * (N + 1)
    
    for i in range(N):
        prefix[i + 1] = prefix[i] + cameras[i]

    for i in range(N):
        left = max(0, i - R)
        right = min(N - 1, i + R)
        shield[i] = prefix[right + 1] - prefix[left]

    diff = [0] * (N + 2)
    window_add = 0

    for i in range(N):
        window_add += diff[i]
        if shield[i] + window_add < target:
            need = target - (shield[i] + window_add)
            pos = min(N - 1, i + R)
            diff[pos] += need
            if pos + 2 * R + 1 < N:
                diff[pos + 2 * R + 1] -= need
            window_add += need
            total_added += need
            if total_added > K:
                return False
    return True


def maximize_weakest_defense(N, R, K, cameras):
    low, high = 0, sum(cameras) + K
    answer = 0
    while low <= high:
        mid = (low + high) // 2
        if can_achieve(mid, N, R, K, cameras):
            answer = mid
            low = mid + 1
        else:
            high = mid - 1
    return answer


# -------- Input ----------
N, R, K = map(int, input().split())
cameras = list(map(int, input().split()))
print(maximize_weakest_defense(N, R, K, cameras))
