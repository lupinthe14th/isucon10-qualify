all: isuumo restart

isuumo: *.go
	go build -o isuumo

restart:
	sudo systemctl restart isuumo.go.service
