# runApp()
runApp()함수는 주어진 위젯을 받아 위젯트리의 루트로 만든다.

아래의 예제에서 위젯트리는 Center위젯과 그 자식인 Child위젯, 두 위젯으로 구성된다.

```flutter
import 'package:flutter/material.dart';

void main() {
  runApp(
    const Center(
      child: Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}
```