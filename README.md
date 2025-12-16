# SRP-1-semester
This code plots a discrete filterless system with a sinusoidal phase detector characteristic, which is directly considered in the paper.

What does the code do?

The dpll_first_order_map function calculates the discrete PLL mapping.

The plot_dpll_map function plots a phase portrait with:

*The plot of the φ_{k+1} = φ_k - K₁ sin(φ_k)

*The identity line (45°)

*The trajectory using the "Lamerey staircase" method

*Fixed points (stable and unstable)


```python 
import numpy as np
import matplotlib.pyplot as plt

def dpll_first_order_map(fi, K1):
    return fi - K1 * np.sin(fi)

def plot_dpll_map(K1, initial_fi, num_iterations):
    # Диапазон значений fi (фазовой ошибки)
    fi_range = np.linspace(-2*np.pi, 2*np.pi, 1000)
    
    # Вычисление fi_{k+1} для каждого fi_k
    fi_next = dpll_first_order_map(fi_range, K1)
    
    # Создание графика
    fig, ax = plt.subplots(figsize=(10, 8))
    
    # 1. График отображения (жирная линия)
    ax.plot(fi_range, fi_next, 'b-', linewidth=3, label=f'φ_{{k+1}} = φ_k - {K1}·sin(φ_k)')
    
    # 2. Линия тождества (45°)
    ax.plot(fi_range, fi_range, 'k--', linewidth=1.5, label='φ_{k+1} = φ_k')
    
    # 3. Построение траектории методом "лестницы Ламерея"
    fi_traj = [initial_fi]
    
    for i in range(num_iterations):
        current_fi = fi_traj[-1]
        next_fi = dpll_first_order_map(current_fi, K1)
        
        # Вертикальная линия от текущей точки до графика отображения
        ax.plot([current_fi, current_fi], [current_fi, next_fi], 'r--', alpha=0.8, linewidth=1)
        
        # Горизонтальная линия от точки на графике до линии тождества
        ax.plot([current_fi, next_fi], [next_fi, next_fi], 'r--', alpha=0.8, linewidth=1)
        
        fi_traj.append(next_fi)
    
    # Отметка начальной точки
    ax.plot(initial_fi, initial_fi, 'ro', markersize=8, label=f'Начало: φ₀ = {initial_fi:.2f}')
    
    # Отметка фиксированных точек (где график пересекает линию тождества)
    # Для нахождения фиксированных точек решаем: φ = φ - K1*sin(φ) => sin(φ)=0
    fixed_points = np.array([-2*np.pi, -np.pi, 0, np.pi, 2*np.pi])
    stable_fixed_points = fixed_points[::2]  # φ = 2nπ - устойчивые при 0<K1<2
    unstable_fixed_points = fixed_points[1::2]  # φ = (2n+1)π - неустойчивые
    
    ax.plot(stable_fixed_points, stable_fixed_points, 'go', markersize=10, label='Устойчивые неподв. точки')
    ax.plot(unstable_fixed_points, unstable_fixed_points, 'yo', markersize=10, label='Неустойчивые неподв. точки')
    
    # Настройки графика
    ax.set_xlabel('φ_k (радианы)', fontsize=14)
    ax.set_ylabel('φ_{k+1} (радианы)', fontsize=14)
    ax.set_title(f'Фазовый портрет дискретной ФАПЧ первого порядка (K₁ = {K1})', fontsize=16)
    
    # Ограничение осей для лучшей видимости
    ax.set_xlim([-(3/2)*np.pi, (3/2)*np.pi])
    ax.set_ylim([-(3/2)*np.pi, (3/2)*np.pi])
    
    # Сетка и легенда
    ax.grid(True, alpha=0.3)
    ax.legend(loc='lower right', fontsize=12)
    ax.axhline(0, color='k', linewidth=0.5, alpha=0.5)
    ax.axvline(0, color='k', linewidth=0.5, alpha=0.5)
    
    # Добавление меток π
    xticks = [-(3/2)*np.pi, -np.pi, -(1/2)*np.pi, 0, (1/2)*np.pi, np.pi, (3/2)*np.pi]
    xtick_labels = ['-3/2п','-π', '-1/2п', '0', '1/2п', 'π', '3/2п']
    ax.set_xticks(xticks)
    ax.set_xticklabels(xtick_labels)
    ax.set_yticks(xticks)
    ax.set_yticklabels(xtick_labels)
    
    # Добавление текста с информацией о режиме
    if 0 < K1 <= 1:
        regime = "Монотонная сходимость"
    elif 1 < K1 < 2:
        regime = "Колебательная сходимость"
    elif 2 < K1 < np.pi:
        regime = "Устойчивый цикл периода 2"
    elif np.pi < K1 < np.sqrt(np.pi**2 + 2):
        regime = "Один из двух локально устойчивых цикла периода 2"
    elif np.sqrt(np.pi**2 + 2) < K1 < 3.5123:
        regime = "Один из двух локально устойчивых цикла периода 4"
    else:
        regime = "Сложный/неустойчивый режим/хаос"
    
    info_text = f"Режим: {regime}\nK₁ = {K1}"
    ax.text(0.02, 0.98, info_text, transform=ax.transAxes,
            fontsize=12, verticalalignment='top',
            bbox=dict(boxstyle='round', facecolor='wheat', alpha=0.8))
    
    plt.tight_layout()
    return fig, ax, fi_traj

# Пример 1 система с асимптотически устойчивым стационарным решением (0 < K1 < 1)
fig1, ax1, traj1 = plot_dpll_map(K1=0.75, initial_fi=2.75, num_iterations=1200)
plt.show()

# Пример 2 система с асимптотически устойчивым стационарным решением (1 < K1 < 2)
fig2, ax2, traj2 = plot_dpll_map(K1=1.85, initial_fi=1.25, num_iterations=1200)
plt.show()

# Пример 3 система асимптотически неустойчива - глобально устойчивый цикл периода 2 (2 < K1 < п)
fig3, ax3, traj3 = plot_dpll_map(K1=2.6, initial_fi=1.90, num_iterations=1200)
plt.show()

# Пример 4 система асимптотически неустойчива - два локально устойчивых цикла периода 2 (2 < K1 < п)
fig4, ax4, traj4 = plot_dpll_map(K1=3.3, initial_fi=1.40, num_iterations=1200)
plt.show()

# Пример 5 система асимптотически неустойчива - два локально устойчивых цикла периода 4 ((п**2 + 2)**0.5 < K1 < 3.5123)
fig5, ax5, traj5 = plot_dpll_map(K1=3.488, initial_fi=1.90, num_iterations=1200)
plt.show()

# Пример 6 система асимптотически неустойчива - хаос (K1 > 4.6)
fig6, ax6, traj6 = plot_dpll_map(K1=4.9, initial_fi=2.10, num_iterations=1200)
plt.show()
