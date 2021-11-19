# SSH

### 基本用法:

- SSH主要用于远程登录。假定你要以用户名user，登录远程主机host，只要一条简单命令就可以了。

  ```shell
  $ ssh user@host
  ```

- 如果本地用户名与远程用户名一致，登录时可以省略用户名。

  ```shell
  $ ssh host
  ```
- SSH的默认端口是22，也就是说，你的登录请求会送进远程主机的22端口。使用p参数，可以修改这个端口。
  ```shell
  $ ssh -p 2222 user@host
  ```

  上面这条命令表示，ssh直接连接远程主机的2222端口。



### 口令登录:

- 如果你是第一次登录对方主机，系统会出现下面的提示：

    ```shell
    $ ssh user@host
    
    　　The authenticity of host 'host (12.18.429.21)' can't be established.
    
    　　RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
    
    　　Are you sure you want to continue connecting (yes/no)?
    　　
    这段话的意思是，无法确认host主机的真实性，只知道它的公钥指纹，问你还想继续连接吗？
    
    所谓"公钥指纹"，是指公钥长度较长（这里采用RSA算法，长达1024位），很难比对，所以对其进行MD5计算，将它变成一个128位的指纹。
    上例中是98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d，再进行比较，就容易多了。
    
    很自然的一个问题就是，用户怎么知道远程主机的公钥指纹应该是多少？回答是没有好办法，远程主机必须在自己的网站上贴出公钥指纹，以便用户自行核对。
    ```

- 假定经过风险衡量以后，用户决定接受这个远程主机的公钥。

	```shell
	Are you sure you want to continue connecting (yes/no)? yes
	```

- 系统会出现一句提示，表示host主机已经得到认可。

  ```shell
  Warning: Permanently added 'host,12.18.429.21' (RSA) to the list of known hosts.
  ```

- 然后，会要求输入密码。

  ```shell
  Password: (enter password)
  ```

  如果密码正确，就可以登录了。

- 当远程主机的公钥被接受以后，它就会被保存在文件$HOME/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。每个SSH用户都有自己的known_hosts文件，此外系统也有一个这样的文件，通常是/etc/ssh/ssh_known_hosts，保存一些对所有用户都可信赖的远程主机的公钥。



### 公钥登录:

- 使用密码登录，每次都必须输入密码，非常麻烦。好在SSH还提供了公钥登录，可以省去输入密码的步骤。

- 所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

- 这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接用ssh-keygen生成一个：

  ```shell
  $ ssh-keygen
  ```

- 运行上面的命令以后，系统会出现一系列提示，可以一路回车。其中有一个问题是，要不要对私钥设置口令（passphrase），如果担心私钥的安全，这里可以设置一个。

- 运行结束以后，在$HOME/.ssh/目录下，会新生成两个文件：id_rsa.pub和id_rsa。前者是你的公钥，后者是你的私钥。

- 这时再输入下面的命令，将公钥传送到远程主机host上面：

  ```shell
  $ ssh-copy-id user@host
  ```

- 好了，从此你再登录，就不需要输入密码了。

- 如果还是不行，就打开远程主机的/etc/ssh/sshd_config这个文件，检查下面几行前面"#"注释是否取掉。

  ```shell
  RSAAuthentication yes
  PubkeyAuthentication yes
  AuthorizedKeysFile .ssh/authorized_keys
  ```

- 然后，重启远程主机的ssh服务。

  ```shell
  // ubuntu系统
  service ssh restart
  
  // debian系统
  /etc/init.d/ssh restart
  ```



### authorized_keys文件:

- 远程主机将用户的公钥，保存在登录后的用户主目录的$HOME/.ssh/authorized_keys文件中。公钥就是一段字符串，只要把它追加在authorized_keys文件的末尾就行了。

- 这里不使用上面的ssh-copy-id命令，改用下面的命令，解释公钥的保存过程：

  ```shell
  $ ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
  ```

- 这条命令由多个语句组成，依次分解开来看：

  - （1）"$ ssh user@host"，表示登录远程主机；
  - （2）单引号中的mkdir .ssh && cat >> .ssh/authorized_keys，表示登录后在远程shell上执行的命令；
  - （3）"$ mkdir -p .ssh"的作用是，如果用户主目录中的.ssh目录不存在，就创建一个；
  - （4）'cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub的作用是，将本地的公钥文件~/.ssh/id_rsa.pub，重定向追加到远程文件authorized_keys的末尾。

- 写入authorized_keys文件后，公钥登录的设置就完成了。



## go用法:

### 连接:

```go
import 	"golang.org/x/crypto/ssh"

// ssh连接方法
func sshconnect(user, password, host string, port int) (*ssh.Session, error) {
	var (
		auth         []ssh.AuthMethod
		addr         string
		clientConfig *ssh.ClientConfig
		client       *ssh.Client
		session      *ssh.Session
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

	if client, err = ssh.Dial("tcp", addr, clientConfig); err != nil {
		return nil, err
	}

	// create session
	if session, err = client.NewSession(); err != nil {
		return nil, err
	}

	return session, nil
}

func main(){
    session, err := sshconnect("root", "1q2w3e", "192.168.161.128", 22)
	if err != nil {
		log.Fatal(err)
	}
	defer session.Close()
}
```

### 发送指令执行 session.Run():

```go
var b bytes.Buffer
session.Stdout = &b
if err := session.Run("ls"); err != nil {
    panic("Failed to run: " + err.Error())
}
fmt.Println(b.String())
```

### 发送指令执行 session.Output():

session.run(command)是直接在host执行命令，不关心执行结果。session.Output是将执行命令之后的Stdout返回

```go
result, err := session.Output("ls -al")
if err != nil {
    fmt.Fprintf(os.Stdout, "Failed to run command, Err:%s", err.Error())
    os.Exit(0)
}
fmt.Println(string(result))
```

### 模拟交互terminal:

```go
session.Stdout = os.Stdout // 会话输出关联到系统标准输出设备
session.Stderr = os.Stderr // 会话错误输出关联到系统标准错误输出设备
session.Stdin = os.Stdin   // 会话输入关联到系统标准输入设备
modes := ssh.TerminalModes{
    ssh.ECHO:          0,  // 禁用回显（0禁用，1启动）
    ssh.TTY_OP_ISPEED: 14400, // input speed = 14.4kbaud
    ssh.TTY_OP_OSPEED: 14400, //output speed = 14.4kbaud
}
if err = session.RequestPty("linux", 32, 160, modes); err != nil {
    log.Fatalf("request pty error: %s", err.Error())
}
if err = session.Shell(); err != nil {
    log.Fatalf("start shell error: %s", err.Error())
}
if err = session.Wait(); err != nil {
    log.Fatalf("return error: %s", err.Error())
}
```

