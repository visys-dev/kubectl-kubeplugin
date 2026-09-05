# kubeplugin

`kubeplugin` — Bash-based plugin для `kubectl`, який отримує CPU та Memory statistics для Pod'ів у вказаному Kubernetes namespace.

Плагін використовує Kubernetes Metrics API через команду:

```bash
kubectl top pod
```

## Requirements

Для роботи необхідні:

* Kubernetes cluster
* `kubectl`
* налаштований Kubernetes context
* Metrics Server

Перевірити доступність metrics:

```bash
kubectl top pods -n kube-system
```

Якщо команда повертає CPU та Memory usage для Pod'ів, plugin готовий до використання.

## Repository structure

```text
.
├── README.md
└── scripts/
    ├── kubeplugin
    └── kubeplugin-output.txt
```

## Usage

Зробити script executable:

```bash
chmod +x scripts/kubeplugin
```

Запустити для namespace `kube-system`:

```bash
./scripts/kubeplugin kube-system
```

Також можна явно передати resource:

```bash
./scripts/kubeplugin kube-system pod
```

Формат виводу:

```text
Resource, Namespace, Name, CPU, Memory
```

Приклад:

```text
Resource, Namespace, Name, CPU, Memory
Pod, kube-system, coredns-..., 2m, 18Mi
Pod, kube-system, metrics-server-..., 3m, 24Mi
```

## Test as kubectl plugin

Для тестування plugin не обов'язково встановлювати в систему постійно.

Створити тимчасову директорію:

```bash
mkdir -p /tmp/kubectl-plugins
```

Скопіювати script під ім'ям, яке відповідає формату `kubectl` plugins:

```bash
cp scripts/kubeplugin /tmp/kubectl-plugins/kubectl-kubeplugin
chmod +x /tmp/kubectl-plugins/kubectl-kubeplugin
```

Перевірити, що `kubectl` знаходить plugin:

```bash
PATH="/tmp/kubectl-plugins:$PATH" kubectl plugin list
```

Очікуваний результат міститиме:

```text
/tmp/kubectl-plugins/kubectl-kubeplugin
```

Запустити plugin:

```bash
PATH="/tmp/kubectl-plugins:$PATH" \
kubectl kubeplugin kube-system
```

Плагін повинен повернути statistics у форматі:

```text
Resource, Namespace, Name, CPU, Memory
Pod, kube-system, <pod-name>, <cpu>, <memory>
```

Після тестування тимчасову директорію можна видалити:

```bash
rm -rf /tmp/kubectl-plugins
```

## Real cluster output

Плагін протестовано на реальному k3s cluster для namespace `kube-system`.

Команда:

```bash
./scripts/kubeplugin kube-system
```

Реальний результат виконання збережено у файлі:

```text
scripts/kubeplugin-output.txt
```

Переглянути результат:

```bash
cat scripts/kubeplugin-output.txt
```

Формат output:

```text
Resource, Namespace, Name, CPU, Memory
```

## Commit

Додати файли до Git:

```bash
git add README.md scripts/kubeplugin scripts/kubeplugin-output.txt
```

Створити commit:

```bash
git commit -m "feat: add kubectl metrics plugin"
```

Відправити зміни у repository:

```bash
git push origin main
```
