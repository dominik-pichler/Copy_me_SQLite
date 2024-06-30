```
.---------------------------------.           
|  .---------------------------.  |           
|[]|                           |[]|           
|  |                           |  |           
|  |                           |  |           
|  |                           |  |           
|  |                           |  |           
|  |                           |  |           
|  |                           |  |           
|  |                           |  |           
|  |                           |  |           
|  `---------------------------'  |           
|      __________________ _____   |           
|     |   ___            |     |  |           
|     |  |   |           |     |  |           
|     |  |   |           |     |  |           
|     |  |   |           |     |  |           
|     |  |___|           |     |  |           
\_____|__________________|_____|__|dp
```


# All about SQLite, Docker and PV's
Template for creating docker based container with an SQLite DB and a PV Storage


## What it does: 

Builds a lightweight linux container (Alpine Linx) and installs SQLite and Python3.
Then it runs the sqlite.py file. 
The data in the sqlite.py file gets then also stores as docker volume and is thereby persistent	even when the container is killed.

> So it simply provides a SQLContainer that runs the given python script and persists the data to the local machine on which the container is running.



