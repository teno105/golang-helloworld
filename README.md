아래는 실습 순서에 맞춰 다시 작성한 `README.md`입니다.

---

# HelloWorld

`HelloWorld`는 Golang으로 작성된 간단한 애플리케이션으로, 기본적인 Application의 구조와 테스트 방법을 익히기 위한 실습입니다.


## 프로젝트 구조

```plaintext
helloworld/
│
├── cmd/
│   └── helloworld/
│       └── main.go
│
├── pkg/
│   └── greeter/
│       └── greeter.go
│
├── go.mod
├── Makefile
└── README.md
```

## 요구 사항

- **Go 언어**가 설치되어 있어야 합니다. [Go 설치 가이드](https://golang.org/doc/install)
- **Git**이 설치되어 있어야 합니다.
- 선택사항: **`golangci-lint`** 설치 ([설치 가이드](https://golangci-lint.run/usage/install/))

## 실습 순서

### 1. 패키지 구조를 위한 디렉토리 생성

먼저 프로젝트 디렉터리를 설정하고 필요한 디렉터리들을 생성합니다.

```bash
mkdir helloworld
cd helloworld
go mod init helloworld

mkdir -p cmd/helloworld
mkdir -p pkg/greeter
```

### 2. `main.go` 생성

`cmd/helloworld/` 디렉터리 아래에 `main.go` 파일을 생성하고, 간단한 `Hello, World!` 메시지를 출력하는 코드를 작성합니다.

```go
// cmd/helloworld/main.go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

이 코드를 통해 프로그램을 실행하면 `Hello, World!`가 출력됩니다.

### 3. `Makefile` 작성

이제 프로젝트의 빌드 및 실행을 자동화하기 위한 `Makefile`을 프로젝트 루트에 작성합니다.

```makefile
# Go 관련 변수 설정
APP_NAME := helloworld
CMD_DIR := ./cmd/helloworld
BUILD_DIR := ./build

.PHONY: all clean build run test fmt vet install

all: build

# 빌드 명령어
build:
	@echo "==> Building $(APP_NAME)..."
	@mkdir -p $(BUILD_DIR)
	go build -o $(BUILD_DIR)/$(APP_NAME) $(CMD_DIR)

# 실행 명령어
run: build
	@echo "==> Running $(APP_NAME)..."
	@$(BUILD_DIR)/$(APP_NAME)

# 코드 포맷팅
fmt:
	@echo "==> Formatting code..."
	go fmt ./...

# 코드 분석
vet:
	@echo "==> Running go vet..."
	go vet ./...

# 의존성 설치
install:
	@echo "==> Installing dependencies..."
	go mod tidy

# 테스트 실행
test:
	@echo "==> Running tests..."
	go test -v ./...

# 빌드 정리
clean:
	@echo "==> Cleaning build directory..."
	rm -rf $(BUILD_DIR)
```

`Makefile`을 이용하여 코드를 빌드하고 실행할 수 있습니다.

```bash
make run
```

이 명령어를 통해 `main.go`에서 작성한 `Hello, World!` 메시지를 확인할 수 있습니다.

### 4. `greeter` 패키지 작성

이제 `pkg/greeter/` 디렉터리에 `greeter.go` 파일을 추가하고, `Greeter` 구조체와 메서드를 작성합니다.

```go
// pkg/greeter/greeter.go
package greeter

import "fmt"

// Greeter 구조체 정의
type Greeter struct {
    Name string
}

// NewGreeter는 새로운 Greeter 객체를 생성하는 함수
func NewGreeter(name string) *Greeter {
    return &Greeter{Name: name}
}

// Greet는 Greeter 구조체의 메서드로, 인사를 출력함
func (g *Greeter) Greet() {
    fmt.Printf("Hello, %s! Welcome to the Go project.\n", g.Name)
}
```

그리고 `main.go` 파일을 수정하여 `greeter` 패키지를 사용하도록 합니다.

```go
// cmd/helloworld/main.go
package main

import "helloworld/pkg/greeter"

func main() {
    // Greeter 패키지를 사용하여 인사 메시지 출력
    greeter := greeter.NewGreeter("Potato")
    greeter.Greet()
}
```

이제 `make run` 명령을 사용하면 `Hello, Potato! Welcome to the Go project.` 메시지가 출력됩니다.

```bash
make run
```

### 5. Unit Test 작성

`pkg/greeter/greeter.go`에 대한 유닛 테스트를 작성하여 코드를 검증합니다. `pkg/greeter` 디렉터리에 `greeter_test.go` 파일을 추가합니다.

```go
// pkg/greeter/greeter_test.go
package greeter

import "testing"

func TestNewGreeter(t *testing.T) {
    name := "Potato"
    g := NewGreeter(name)
    
    if g.Name != name {
        t.Errorf("expected %s, got %s", name, g.Name)
    }
}

func TestGreeterGreet(t *testing.T) {
    g := NewGreeter("Potato")
    g.Greet()
    // 여기서는 출력을 확인하는 테스트이므로, 별도 로직을 추가할 수 있음
}
```

테스트를 실행하려면 다음 명령어를 사용합니다.

```bash
make test
```
