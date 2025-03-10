어떤 container가 어떤 volume을 mount하고 있는지 아래 명령어를 사용하여 확인할 수 있다.
```zsh
docker ps -a --format '{{.Names}}' | xargs -I {} docker inspect -f '{{.Name}}{{range .Mounts}}{{if eq .Type "volume"}} has volume {{.Name}}{{end}}{{end}}' {}
```

`docker inspect`에서,  `-f` 는 format으로, go template을 인자로 받는다. 해당 go template은 아래와 같은 의미를 가진다.
```go
{{.Name}}{{range .Mounts}}{{if eq .Type "volume"}} has volume {{.Name}}{{end}}{{end}}
```

1. `{{.Name}}`: This prints the name of the container.
2. `{{range .Mounts}}...{{end}}`: This starts a loop over all the mounts of the container. Everything between range and end will be executed for each mount.
3. `{{if eq .Type "volume"}}...{{end}}`: This is a conditional statement. It checks if the type of the current mount is "volume". The eq function checks for equality.
4. `has volume {{.Name}}`: If the condition is true (i.e., the mount type is "volume"), this text is printed along with the name of the volume.