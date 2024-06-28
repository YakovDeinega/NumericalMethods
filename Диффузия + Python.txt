import numpy as np
import matplotlib.pyplot as plt
from matplotlib import cm

def solve_diffusion_2d(alpha, dx, dy, dt, nx, ny, nt, u_initial):
    # Копируем начальное условие в рабочий массив
    u = np.zeros((nx, ny))
    for i in range(nx):
        for j in range(ny):
            u[i][j] = u_initial[i][j]

    # Коэффициенты для явной схемы
    dx2 = dx * dx
    dy2 = dy * dy
    dt_alpha = dt * alpha
    print("t=0")
    for i in range (nx):
        for j in range (ny):
            print(u[i][j],end=" ")
        print()
    for n in range(1, nt + 1):
        # Копируем предыдущий шаг
        u_prev = np.zeros((nx, ny))
        for i in range(nx):
            for j in range(ny):
                u_prev[i][j] = u[i][j]

        # Обновляем значения для внутренней области
        for i in range(1, nx - 1):
            for j in range(1, ny - 1):
                u[i][j] = (
                    u_prev[i][j]
                    + dt_alpha * (
                        (u_prev[i + 1][j] - 2 * u_prev[i][j] + u_prev[i - 1][j]) / dx2
                        + (u_prev[i][j + 1] - 2 * u_prev[i][j] + u_prev[i][j - 1]) / dy2
                    )
                )

        # Граничные условия (если необходимо, например, нулевые)
        temp=0
        for i in range(nx):
            u[i][0] = (4 * temp) * (1 - temp)
            temp += dx
            u[i][ny - 1] = 0
        for j in range(ny):
            u[0][j] = 0
            u[nx - 1][j] = 0
        if(n==500):
            print("t=500")
            for i in range(nx):
                for j in range(ny):
                    print(u[i][j], end=" ")
                print()

    return u

# Параметры
alpha = 1  # Коэффициент диффузии
lx = 1  # Длина области по x
ly = 1  # Длина области по y
nx = 11  # Число узлов по x
ny = 11 # Число узлов по y
dx = lx / (nx - 1)  # Шаг по x
dy = ly / (ny - 1)  # Шаг по y
dt = 0.0001  # Шаг по времени
nt = 500  # Число временных шагов
print(dx)
# Начальное условие (например, функция Гаусса в центре)
u_initial = np.zeros((nx, ny))
x = np.linspace(0, lx, nx)
y = np.linspace(0, ly, ny)
X, Y = np.meshgrid(x, y)
temp=0
for i in range (nx):
    u_initial[i][0]=(4*temp)*(1-temp)
    temp+=dx

# Решение
u_final = solve_diffusion_2d(alpha, dx, dy, dt, nx, ny, nt, u_initial)
print(u_final)
# Визуализация
plt.figure(figsize=(8, 6))
plt.contourf(X, Y, u_final, cmap=cm.viridis)
plt.colorbar()
plt.xlabel('x')
plt.ylabel('y')
plt.title('Распределение концентрации после {} временных шагов'.format(nt))
plt.show()