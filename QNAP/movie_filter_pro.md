Создание контейнера:
- Точка монтирования:
  - Хост: `/PERMANENT/movie-filter-pro`
  - Контейнер: `/app/data`
- Точка монтирования 2:
  - Хост: `/share/CACHEDEV1_DATA/TorrentsHotFolder`
  - Контейнер: `/torrents_hotfolder`
- Порт
  - Хост: `8008`
  - Контейнер: `8008`


Команда для запуска второй версии:
```
docker run -d \
  --name movie-filter-pro-v2 \
  -p 8009:8008 \
  -v /PERMANENT/movie-filter-pro-v2:/app/data \
  -v /share/CACHEDEV1_DATA/TorrentsHotFolder:/torrents_hotfolder \
  swasher/movie-filter-pro-v2:amd64
```
