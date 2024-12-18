# docker system prune 
- https://alexgallacher.com/prune-unused-docker-images-automatically/
- ```
  docker system prune -a --volumes
  docker system df
  docker volume ls
  docker volume rm $(docker volume ls -qf dangling=true)
  docker system prune -af  --filter "until=$((30*24))h"
  ```
