 **TAULA COMPARATIVA COMPLETA – T04: Apache vs Nginx**


# T04 – Duel de Titans: Apache vs Nginx  
## Taula comparativa de rendiment

| Mètrica                  | Apache prova lleugera | Nginx prova lleugera | Apache prova d’estrès | Nginx prova d’estrès |
|--------------------------|------------------------|-----------------------|------------------------|------------------------|
| Time taken for tests     | 0.983 s                | 0.713 s               | 2.828 s                | 1.044 s                |
| Transfer rate            | 7296.47 Kbytes/sec      | 9979.57 Kbytes/sec    | 25363.76 Kbytes/sec    | 68180.11 Kbytes/sec    |
| Requests per second (RPS)| 1017.10 req/s           | 1401.60 req/s         | 3535.60 req/s          | 9575.70 req/s          |
| Time per request (mean)  | 9.832 ms                | 7.135 ms              | 28.284 ms              | 10.443 ms              |
| Completed requests       | 1000                   | 1000                  | 10000                  | 10000                  |
| Failed requests          | 0                      | 0                     | 0                      | 0                      |


**Alumnes:** Santiago Hernández, Santiago Moreno  


***

# ANÁLISIS RÁPIDO 
*   **Nginx** es más rápido en TODAS las métricas.
*   En estrés, Nginx aguanta **casi 3 veces más RPS** que Apache.
*   Apache sufre más latencia y baja más el rendimiento al aumentar la concurrencia.
*   Ambos completan todas las peticiones sin errores (0 failed).
