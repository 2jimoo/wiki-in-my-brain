# docker system prune 
- https://alexgallacher.com/prune-unused-docker-images-automatically/
- ```
  docker system prune -a --volumes
  docker system df
  docker volume ls
  docker volume rm $(docker volume ls -qf dangling=true)
  docker system prune -af  --filter "until=$((30*24))h"
  ```


# mirror hub는 되는데 docker registry 직접 사용은 안 될 때
- mirror hub 주소를 HUB_IMAGE_NAME_PREFIX 환경변수로 github actions에 등록
