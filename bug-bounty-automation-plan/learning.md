# Learning

## Perfect

1. Paralel Execution (Task Runner)
   1. Concurrent: Menjalankan banyak task dalam satu flow secara bersamaan
   2. Distributed: Menjalankan task di berbagai container atau bahkan VPS berbeda
2.  Flow Control

    "Jalankan Subfinder dan Assetfinder barengan, tapi jangan jalankan httpx sebelum selesai keduanya"
3.  Multi Tenancy (banyak target sekaligus)

    Bukan cuma task-nya yang bisa barengan, tapi Flow-nya juga.

    1. Jam 14.00 kamu input `target-A.com`.
    2. Jam 14.01 kamu input `target-B.com`. Prefect bisa menjalankan kedua flow ini secara bersamaan (selama RAM VPS kamu kuat). Prefect akan mengatur antreannya dengan sangat cantik di dashboard-nya.

