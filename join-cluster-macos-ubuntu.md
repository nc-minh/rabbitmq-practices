# Yêu cầu

- MacOS, Ubuntu, kết nối chung mạng.

# Ubuntu

## Cài đặt rabbitmq trên máy

1. Cài đặt các phụ thuộc:

```
sudo apt-get update
sudo apt-get install -y curl gnupg apt-transport-https
```

2. Thêm repository của RabbitMQ:

```
curl -fsSL https://packages.erlang-solutions.com/erlang-solutions_2.0_all.deb -o erlang-solutions_2.0_all.deb
sudo dpkg -i erlang-solutions_2.0_all.deb
sudo apt-get update
sudo apt-get install -y erlang
```

```
curl -fsSL https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey -o rabbitmq-signing-key-public.asc
sudo apt-key add rabbitmq-signing-key-public.asc
echo "deb https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/rabbitmq.list
sudo apt-get update
sudo apt-get install -y rabbitmq-server

```

3. Kích hoạt plugin quản lý

```
sudo rabbitmq-plugins enable rabbitmq_management
```

4. Khởi động RabbitMQ:

```
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
```

5. Kiểm tra

```
http://localhost:15672/
```

## Cấu hình mạng

1. Tắt tường lửa

```
sudo ufw allow proto tcp from any to any port 5672
sudo ufw allow proto tcp from any to any port 4369
sudo ufw allow proto tcp from any to any port 25672
sudo ufw reload
```

2. Cấu hình hosts

- Mở file `/etc/hosts`
- Thêm ipv4 của mạng MacOS và Ubuntu
- Vi dụ:

```
192.168.31.41 rabbit-2
192.168.31.228 rabbit-1
```

- 192.168.31.228 là ipv4 của ubuntu
- 192.168.31.41 là ipv4 của macos

3. Khởi động lại máy

# MacOS

## Cài đặt rabbitmq trên máy

1. Cài đặt rabbitmq:

```
brew install rabbitmq
```

2. Khởi động rabbitmq

```
brew services start rabbitmq
```

3. Khởi động rabbitmq

```
export PATH=$PATH:/usr/local/sbin
```

4. Kiểm tra

```
rabbitmqctl status
```

```
http://localhost:15672/
```

## Cấu hình mạng

1. Tắt tường lửa

2. Cấu hình hosts

- Mở file `/etc/hosts`
- Thêm ipv4 của mạng MacOS và Ubuntu
- Vi dụ:

```
192.168.31.41 rabbit-2
192.168.31.228 rabbit-1
```

- 192.168.31.228 là ipv4 của ubuntu
- 192.168.31.41 là ipv4 của macos

3. Set hostname

```
sudo hostnamectl set-hostname rabbit-2 --static
```

```
sudo scutil --set HostName rabbit-2
```

```
dscacheutil -flushcache
```

```
scutil --get HostName
```

3. Khởi động lại máy

# Join cluster

1. Đồng bộ erlang cookie

- Ubuntu: `sudo cat /var/lib/rabbitmq/.erlang.cookie`
- Mac: `cat /usr/local/var/lib/rabbitmq/.erlang.cookie`

Có thể copy từ ubuntu qua macos hoặc người lại:
Ví dụ copy từ ubuntu qua macos

```
echo "YOUR_COPIED_COOKIE" > /usr/local/var/lib/rabbitmq/.erlang.cookie
```

2. Join cluster

- Nếu bên nào là main node thì stop node ở đó

```
sudo rabbitmqctl stop_app
sudo rabbitmqctl reset
sudo rabbitmqctl join_cluster rabbit@rabbit-1
sudo rabbitmqctl start_app

```
