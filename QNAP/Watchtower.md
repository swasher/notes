Через пакет стандартную версию watchtower невозможно настроить хук на произвольном порту. А порт 8080 на qnap занят. Поэтому используем обязательно форк. Устанавливаем так (без `sudo`):

    # docker run -d \
      --name watchtower \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -p 9090:9090 \
      --restart unless-stopped \
      nickfedor/watchtower \
      --http-api-update \
      --http-api-port=9090 \
      --http-api-token=<MY-SECRET-TOKEN>

Затем, чтобы запустить обновление, можно написать 

    # curl -H "Authorization: Bearer <MY-SECRET-TOKEN>" qnap:9090/v1/update
