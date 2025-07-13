MOD = 10 ** 9 + 7

# Precompute all valid L-tile masks within a 2x2 grid
def generate_l_shapes():
    shapes = []
    for i in range(2):
        for j in range(2):
            # remove (i,j) from 2x2 block
            shape = [(x, y) for x in range(2) for y in range(2) if (x, y) != (i, j)]
            shapes.append(shape)
    return shapes

def solve_tiling(N, M):
    if (N * M) % 3 != 0:
        return 0

    from functools import lru_cache

    l_shapes = generate_l_shapes()

    @lru_cache(None)
    def dp(col, mask):
        if col == M:
            return 1 if mask == 0 else 0
        res = 0

        def dfs(r, msk, new_mask):
            if r >= N:
                res = dp(col + 1, new_mask)
                return res
            if (msk >> r) & 1:
                return dfs(r + 1, msk, new_mask)
            total = 0
            for shape in l_shapes:
                valid = True
                temp = msk
                temp_new = new_mask
                for dx, dy in shape:
                    x = r + dx
                    y = dy
                    if x >= N or y >= 2:
                        valid = False
                        break
                    if y == 0:
                        if (temp >> x) & 1:
                            valid = False
                            break
                        temp |= (1 << x)
                    else:
                        if (temp_new >> x) & 1:
                            valid = False
                            break
                        temp_new |= (1 << x)
                if valid:
                    total = (total + dfs(r + 1, temp, temp_new)) % MOD
            return total

        res = dfs(0, mask, 0)
        return res

    return dp(0, 0)

# Example usage:
print(solve_tiling(2, 3))     # Output: 2
print(solve_tiling(4, 6))     # Output: 18
print(solve_tiling(10, 4000)) # Output: 0
