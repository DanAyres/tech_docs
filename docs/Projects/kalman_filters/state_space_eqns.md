# State Space Equations for Linear Dynamic Systems

A general system of linear first order differential equations may be written as:

\begin{equation}
\frac{dx}{dt} = A(t)x(t) + B(t)u(t)
\end{equation}

where $x = [x_1, x_2, \cdots, x_k]^T$ is the state-space vector and $u = [u_1, u_2, \cdots, u_m]^T$ is the input vector.

\begin{equation}
x(t) = \int_\tau^t e^{A(t-\lambda)}Bu(\lambda)d\lambda + x(\tau)e^{A(t-\tau)}
\end{equation}


which can be written as 

\begin{equation}
x(t) = \Phi(t, \tau)x(\tau) + \int_\tau^t \Phi(t, \lambda)Bu(\lambda)d\lambda
\end{equation}


where

\begin{equation}
\Phi(t, \tau) = e^{A(t-\tau)}
\end{equation}


is the state-transition matrix.
