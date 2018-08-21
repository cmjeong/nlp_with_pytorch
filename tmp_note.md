# Temporal Note for Markdown Equations

## Add one Smoothing

$$
\begin{aligned}
P(w_i|w_{<i}) \approx \frac{C(w_{<i},w_i)+1}{C(w_{<i})+V}
\end{aligned}
$$

$$
\begin{aligned}
P(w_i|w_{<i}) &\approx \frac{C(w_{<i},w_i)+k}{C(w_{<i})+kV} \\
&\approx \frac{C(w_{<i},w_i)+(m/V)}{C(w_{<i})+m}
\end{aligned}
$$

$$
P(w_i|w_{<i}) \approx \frac{C(w_{<i},w_i)+m P(w_i)}{C(w_{<i})+m}
$$

## Auto-regressive and Teacher Forcing on RNNLM

$$
X=argmax_X P(X)=argmax_Y \prod_{i=1}^{n}{P(x_i|x_{<i})}
$$

$$
or
$$
$$
\begin{aligned}
x_i=&argmax_y{P(x|x_{<i})} \\
&\text{where }x_0=BOS
\end{aligned}
$$

$$
\hat{x}_t=argmax_x{P(x_t|x_{<t};\theta)}\text{ where }X=\{x_1,x_2,\cdots,x_n\}
$$

$$
\begin{aligned}
\mathcal{L}(X)&=-\sum_{t=1}^{n+1}{\log{P(x_t|x_{<t};\theta)}} \\
\theta &\leftarrow \theta-\lambda\frac{1}{N}\sum_{i=1}^{N}{\mathcal{L}(X_i)}
\end{aligned}
$$

## Expectation

$$
\begin{aligned}
\mathbb{E}_{x \sim p}[reward(x)]&=\int{reward(x)p(x)}dx \\
&\approx\frac{1}{K}\sum_{i=1}^{K}{reward(x_i)} \\
&\approx reward(x)
\end{aligned}
$$

## Importance Sampling

$$
\begin{aligned}
\mathbb{E}_{X \sim p}[f(x)]&=\int_{x}{f(x)p(x)}dx \\
&=\int_{x}{f(x)\frac{p(x)}{q(x)}q(x)}dx \\
&=\mathbb{E}_{X \sim q}[f(x)\frac{p(x)}{q(x)}]
\end{aligned}
$$