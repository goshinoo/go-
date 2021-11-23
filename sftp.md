# sftp

sftp（Secure File Transfer Protocol）是一种安全的文件传送协议，是ssh内含协议，也就是说只要sshd服务器启动了，sftp就可使用，不需要额外安装，它的默认端口和SSH一样为22。
sftp通过使用加密/解密技术来保障传输文件的安全性，因此sftp的传输效率比普通的FTP要低，但sftp的安全性要比ftp高，因此sftp通常用于报表、对账单等对安全性要求较高的场景。

```go
import "github.com/pkg/sftp"
```

```go
func sftpconnect(user, password, host string, port int) (*sftp.Client, error) {
	var (
		auth         []ssh.AuthMethod
		addr         string
		clientConfig *ssh.ClientConfig
		sshClient    *ssh.Client
		sftpClient   *sftp.Client
		err          error
	)
	// get auth method
	auth = make([]ssh.AuthMethod, 0)
	auth = append(auth, ssh.Password(password))

	clientConfig = &ssh.ClientConfig{
		User:    user,
		Auth:    auth,
		Timeout: 30 * time.Second,
		HostKeyCallback: func(hostname string, remote net.Addr, key ssh.PublicKey) error {
			return nil
		},
	}

	// connet to ssh
	addr = fmt.Sprintf("%s:%d", host, port)

	if sshClient, err = ssh.Dial("tcp", addr, clientConfig); err != nil {
		return nil, err
	}

	// create sftp client
	if sftpClient, err = sftp.NewClient(sshClient); err != nil {
		return nil, err
	}

	return sftpClient, nil
}
```

```go
var (
    sftpClient *sftp.Client
)

// 这里换成实际的 SSH 连接的 用户名，密码，主机名或IP，SSH端口
sftpClient, err := sftpconnect("root", "1q2w3e", "192.168.161.128", 22)
if err != nil {
    log.Fatal(err)
}
defer sftpClient.Close()

// 用来测试的本地文件路径 和 远程机器上的文件夹
var localFilePath = "main.go"
var remoteDir = "/root/"

// 打开需要读出的文件
srcFile, err := os.Open(localFilePath)
if err != nil {
    log.Fatal(err)
}
defer srcFile.Close()

var remoteFileName = path.Base(localFilePath)
// 创建写入的文件
dstFile, err := sftpClient.Create(path.Join(remoteDir, remoteFileName))
if err != nil {
    log.Fatal(err)
}
defer dstFile.Close()

buf := make([]byte, 1024)
for {
    // 读出数据放入切片
    n, _ := srcFile.Read(buf)
    // n=0说明文件读完
    if n == 0 {
        break
    }
    // 将切片数据放入目标文件
    dstFile.Write(buf[0:n])
}

fmt.Println("copy file to remote server finished!")
```

