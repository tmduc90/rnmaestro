# 🤖 Kiểm thử ứng dụng di động dễ dàng hơn với Maestro 👽

<p align="center">
  <a href="https://github.com/tuantvk/rnmaestro/issues">
    <img src="https://img.shields.io/github/issues/tuantvk/rnmaestro.svg" alt="issues" />
  </a>
  <a href="#">
    <img src="https://img.shields.io/github/forks/tuantvk/rnmaestro.svg" alt="forks" />
  </a>
  <a href="#">
    <img src="https://img.shields.io/github/stars/tuantvk/rnmaestro.svg" alt="stars" />
  </a>
  <a href="https://github.com/tuantvk/rnmaestro/blob/master/LICENSE">
    <img src="https://img.shields.io/github/license/tuantvk/rnmaestro.svg" alt="LICENSE" />
  </a>
</p>

| ![Maestro Twitter Example](https://559345148-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fn5KVIOjVkVjYRyVWZ0yT%2Fuploads%2FBdkVbb4VQTkL4zLm6nvm%2Ftwitter_continuous_v3_fast.gif?alt=media&token=8a812b85-3b44-44f0-9137-3e74293b1acc) | 
|:--:| 
| *Maestro Twitter Example - maestro.mobile.dev* |


Tôi đã từng sử dụng [Detox](https://wix.github.io/Detox/) để kiểm thử các ứng dụng viết bằng React Native. Tại thời điểm đó Detox khá "hịn" và bá đạo, tiết kiệm được cả khối thời gian cũng như công sức của đội dev và đội kiểm thử. Tuy nhiên sau này, tôi thấy độ phức tạp, cũng như độ "khó" với các thành viên mới trong team, đó là thời điểm **Maestro** đến với tôi như một vị cứu tinh. "Xạo quần" tí thôi chứ tôi biết Maestro qua một bài viết trên trang [dev.to](https://dev.to/), nhưng team tôi "gà" là sự thật 😂.


### Nội dung:
* [Maestro là cái gì ?](#maestro-là-cái-gì)
* [Cài đặt môi trường và dự án React Native](#cài-đặt-môi-trường-và-dự-án-react-native)
* [Cài đặt Maestro](#cài-đặt-maestro)
* [Mô tả các bước kiểm thử](#mô-tả-các-bước-kiểm-thử)
* [Maestro studio](#maestro-studio)
* [Test case](#test-case)
* [Kiểm tra phần tử bằng testID](#kiểm-tra-phần-tử-bằng-testid)
* [Sử dụng biến](#sử-dụng-biến)
* [runFlow](#runflow)
* [Quay màn hình](#quay-màn-hình)
* [Tags](#tags)
* [Kiểm thử trên cloud](#kiểm-thử-trên-cloud)
* [Videos kiểm thử của Maestro](#videos-kiểm-thử-của-maestro)


## Maestro là cái gì ?

Tóm cái váy lại, [Maestro](https://maestro.mobile.dev/) là một framework giúp kiểm thử giao diện người dùng (UI) đơn giản và hiệu quả. Maestro dựa trên ý tưởng từ những người đàn anh đi trước như: Appium, Espresso, UIAutomator, XCTest. Sự khác biệt chủ yếu ở đây là Maestro viết kiểm thử theo dạng Flows.

Flows là gì? Nôm na, Flows sẽ giống như một hành trình đi tìm ánh sáng phía cuối con hẻm cụt, đi từng bước từng bước qua những ngôi nhà, các bước kiểm thử được viết trong file `yaml` hoặc `yml`. Nó giống như việc ta ra lệnh cho máy biết phải làm gì và kiểm tra gì. Đọc thêm tại [Why Maestro?](https://maestro.mobile.dev/#why-maestro)

Maestro hỗ trợ các nền tảng như:

| Platform     | Support |
|--------------| --------|
| iOS          | ✅ |
| Android      | ✅ |
| React Native | ✅ |
| Flutter      | ✅ |
| Web Views    | ✅ |

Cá nhân tôi rất thích sử dụng Maestro cho kiểm thử ứng dụng di động. Quá trình cài đặt, viết kiểm thử cũng hết sức dễ dàng với tất cả những ai chưa biết sử dụng máy tính Casio FX-570.
Trong ví dụ này, tôi sẽ hướng dẫn các bạn cài đặt và viết một vài test case phổ biến. Tôi sử dụng Mac OS và ứng dụng đơn giản viết bằng React Native.


## Cài đặt môi trường và dự án React Native

Đầu tiên, tất nhiên là bạn phải có ứng dụng cần kiểm thử rồi. Để tạo dự án React Native có thể tham khảo các bước đầy đủ tại [Setting up the development environment](https://reactnative.dev/docs/environment-setup).

Giả sử bạn đã có môi trường, tiến hành khởi tạo ứng dụng:

```sh
npx react-native init RNMaestro
```

Sau khi khởi tạo xong bạn để ý **applicationId** của Android (`android/app/build.gradle -> applicationId`) và iOS (`Dự án trong Xcode -> Signing & Capabilities -> Bundle Identifier`). Bạn có thể tuỳ ý chỉnh sửa chúng để sử dụng sau này, trong hướng dẫn này tôi chỉnh sửa Android và iOS thành `com.rnmaestro`.

Tại file `App.tsx` của dự án, bạn copy & paste đoạn code phía dưới.

```js
// App.tsx
import React, { useState } from 'react';
import {
  View,
  Alert,
  SafeAreaView,
  TextInput,
  Button,
  FlatList,
} from 'react-native';

const TASKS = Array.from({ length: 25 }, (_, i) => ({ title: 'Task ' + i }));

interface Item {
  title: string;
}

const App = () => {
  const [title, setTitle] = useState('');
  const [tasks, setTasks] = useState(TASKS);

  const addTask = () => {
    if (title?.trim()?.length === 0) {
      Alert.alert('Title is required');
    } else {
      const newTasks = [...tasks];
      newTasks.unshift({ title });
      setTasks(newTasks);
      setTitle('');
    }
  };

  const renderItem = ({ item }: { item: Item }) => (
    <Button title={item?.title} onPress={() => Alert.alert(item?.title)} />
  );

  const keyExtractor = (item: Item, idx: number) => `${idx}`;

  return (
    <SafeAreaView>
      <FlatList
        data={tasks}
        renderItem={renderItem}
        keyExtractor={keyExtractor}
        ListHeaderComponent={
          <View>
            <TextInput
              value={title}
              placeholder="Enter your title"
              onChangeText={setTitle}
            />
            <Button testID="btn_add_task" title="Add task" onPress={addTask} />
          </View>
        }
      />
    </SafeAreaView>
  );
};

export default App;
```

Hoặc bạn có thể sử dụng dự án của tôi (**bỏ qua bước dưới nếu không cần**):

* Clone dự án

```sh
git clone https://github.com/tuantvk/rnmaestro.git
```

* Cài đặt packages

```sh
cd rnmaestro; yarn install
```

* Pod (**dành cho iOS**)

```sh
npx pod-install
```


## Cài đặt Maestro

Thông tin cài đặt cho Windows hoặc chi tiết các môi trường khác, vui lòng xem thêm ở tài liệu chính gốc [Installing Maestro
](https://maestro.mobile.dev/getting-started/installing-maestro).

Cài đặt trên Mac OS, Linux:

```sh
curl -Ls "https://get.maestro.mobile.dev" | bash
```

Kiểm tra xem em hàng đã cài đặt thành công hay chưa:

```sh
maestro -v
```

Nếu thấy trả về các số dạng `vi.xxx.com` (ví dụ: 1.27.0) là đã thành công!
Trong trường hợp máy báo `zsh: command not found: maestro`, hãy tắt terminal đi rồi mở lại.

Để chạy trên máy ảo trên **iOS**, cần cài đặt thêm [Facebook IDB](https://fbidb.io/):

```sh
brew tap facebook/fb
brew install idb-companion
```

> * Xcode khuyên nên dùng các phiên bản từ 14 trở nên.
> * Một tin chẳng mấy vui, hiện tại, Tháng Năm 2023 Maestro chưa hỗ trợ chạy trên máy thật.

Sau khi hoàn thành xong các bước trên là đã xong phần cài đặt. Bắt đầu vào phần viết test case kiểm thử.

<p align="center">
  <img style="width:450px" src="https://media1.giphy.com/media/26u4lOMA8JKSnL9Uk/giphy.gif" alt="https://media1.giphy.com/media/26u4lOMA8JKSnL9Uk/giphy.gif" />
<p>


## Mô tả các bước kiểm thử

Dựa vào chức năng của ứng dụng hiện tại, sẽ có một vài bước như sau:

1. Mở ứng dụng lên
2. Nhấn nút `Add task` xem đã kiểm tra rỗng `TextInput` chưa
3. Kiểm tra thông báo nếu rỗng
4. Nhập `title`
5. Nhấn nút `Add task`
6. Kiểm tra task mới đã có chưa


## Maestro studio

<figure><img src="https://raw.githubusercontent.com/mobile-dev-inc/maestro-docs/main/.gitbook/assets/Screenshot%202023-03-10%20at%2013.23.54.png" alt=""><figcaption></figcaption></figure>

Để dễ dàng xem được phần tử trong ứng dụng hoặc chạy trực tiếp các câu lệnh trên trình duyệt, ta có thể sử dụng **Maestro studio**.

```sh
maestro studio
```

Sau khi chạy lệnh trên maestro sẽ mở một tab trên trình duyệt, mặc định sẽ là `http://localhost:9999`.

https://user-images.githubusercontent.com/30563960/236864010-3700e3c4-9fb8-4cee-bf59-b2755b3ae273.mp4


## Test case

Tại thư mục gốc của dự án, tôi tạo file với đường dẫn `.maestro/app.yaml`.

```yaml
# .maestro/app.yaml
appId: com.rnmaestro # applicationId
---
- launchApp
# Kiểm tra hiện thông báo "Title is required"
- tapOn: "Add task"
- assertVisible: "Title is required"
- tapOn: "OK"

# Kiểm tra thêm task
- tapOn: "Enter your title"
- inputText: "Task from maestro"
- hideKeyboard # Lưu ý 1
- tapOn: "Add task"
- assertVisible: "Task from maestro"
```

> **Lưu ý 1**:
> Trên iOS `hideKeyboard` có thể không ẩn được bàn phím, do vậy, tài liệu hướng dẫn khuyên nên sử dụng `tapOn` nhấn ra ngoài để có thể tắt được bàn phím. Xem thêm [iOS implementation caveat](https://maestro.mobile.dev/api-reference/commands/hidekeyboard#ios-implementation-caveat).

Để chạy file test case tôi sử dụng lệnh:

```sh
# Chạy 1 file duy nhất
maestro test .maestro/app.yaml
# hoặc
# Chạy nhiều file trong thư mục
maestro test .maestro/
```

https://user-images.githubusercontent.com/30563960/236864158-dbf562bc-1a98-4352-972a-e16ff68b8f3b.mp4

Trong terminal sẽ như hình dưới:

<p>
  <img style="width:700px" src="assets/logs.png" alt="tuantvk - maestro logs" />
<p>

Để tự động chạy kiểm thử lại mỗi khi có thay đổi, bạn có thể chạy test với câu lệnh:

```sh
maestro test -c .maestro/app.yaml
```

**Commands**

| | | | |
| ---- | ---- | ---- | ---- |
| assertVisible | assertNotVisible | assertTrue | back |
| clearKeychain | clearState | copyTextFrom | evalScript |
| eraseText | extendedWaitUntil | hideKeyboard | inputText |
| launchApp | openLink | pressKey | pasteText |
| repeat | runFlow | runScript | scroll |
| scrollUntilVisible | setLocation | stopApp | swipe |
| takeScreenshot | tapOn | travel | waitForAnimationToEnd |

Xem chi tiết tại [Maestro - Commands](https://maestro.mobile.dev/api-reference/commands).

### Kiểm tra phần tử bằng `testID`

Trong ví dụ ở trên, tôi đã hướng dẫn viết flow bằng cách gọi trực tiếp vào các nội dung có trong màn hình. Tuy nhiên, sẽ có nhiều phần kiểm thử có nội dung thay đổi sau mỗi lần thao tác, do đó bạn cần phải sử dụng `testID` để xác định như: [View](https://reactnative.dev/docs/view#testid), [Button](https://reactnative.dev/docs/button#testid), [Text](https://reactnative.dev/docs/text#testid), [Image](https://reactnative.dev/docs/image#testid).

Ví dụ:

```yaml
# .maestro/app.yaml
appId: com.rnmaestro # applicationId
---
- launchApp
# Kiểm tra hiện thông báo "Title is required"
- tapOn:
    id: "btn_add_task" # testID ở đây
- assertVisible: "Title is required"
- tapOn: "OK"
```


### Sử dụng biến

Trong trường hợp cần truyền các biến từ bên ngoài vào file flow, ta có thể truyền theo dạng qua các tham số:

```sh
maestro test -e APP_ID=com.rnmaestro .maestro/app.yaml
```

Tại các vị trí sử dụng theo cú pháp `${name}`:

```yaml
# .maestro/app.yaml
appId: ${APP_ID} # applicationId
---
- launchApp
```

Nếu như có quá nhiều biến cần khai báo, ta có thể viết toàn bộ vào key `env` trước dòng `---`:

```yaml
# .maestro/app.yaml
appId: ${APP_ID} # applicationId
env:
  APP_ID: com.rnmaestro
---
- launchApp
```

Bạn muốn chạy kiểm thử từ `scripts` của `package.json` có thể config:

```json
{
  "scripts": {
    "test": "$HOME/.maestro/bin/maestro test",
    "test-dev": "yarn test -e APP_ID=com.rnmaestro.dev",
    "test-prod": "yarn test -e APP_ID=com.rnmaestro"
  }
}
```

Ở đây, tôi ví dụ có 2 môi trường là `dev` và `production`.

* `com.rnmaestro.dev` dành cho môi trường dev
* `com.rnmaestro` dành cho môi trường production

Chạy kiểm thử:

```sh
yarn run test-prod .maestro/app.yaml
```


### runFlow

Nếu như bạn không muốn bị trùng lặp các bước, phải viết đi viết lại 1 đoạn kiểm thử nào đó, bạn có thể sử dụng `runFlow` để thực thi một luồng khác. Ví dụ:

```yaml
# Login.yaml
appId: com.example.app
---
- launchApp
- tapOn: Username
- inputText: Test User
- tapOn: Password
- inputText: Test Password
- tapOn: Login
```

```yaml
# Settings.yaml
appId: com.example.app
---
- runFlow: Login.yaml # Chạy kiểm thử từ file `Login.yaml`
- tapOn: Settings
- assertVisible: Switch to dark mode
```

Xem thêm tại [Maestro - runFlow](https://maestro.mobile.dev/api-reference/commands/runflow).


### Quay màn hình

Để quay lại quá trình kiểm thử tôi sử dụng lệnh:

```sh
maestro record .maestro/app.yaml
```

Sau khi quá trình kiểm thử hoàn tất, maestro sẽ xuất ra một video định dạng `mp4` ghi lại toàn bộ quá trình.

> Hiện tại, Maestro các phiên bản `CLI 1.26.0`, `CLI 1.26.1`, `CLI 1.27.0` tôi thấy tính năng `record` đang bị lỗi trên iOS, tuy nhiên đã được fix tại commit [2bd380d](https://github.com/mobile-dev-inc/maestro/commit/2bd380da5cb068da5704f313711530d89e0ba74f), nhưng chưa thấy release. Nếu bạn đang sử dụng các phiên bản trên, có thể tính năng quay màn hình sẽ không hoạt động (Ngày cập nhật: 2023-05-09).


## Tags

Trong trường hợp bạn chỉ kiểm thử (`--include-tags`) hoặc loại bỏ (`--exclude-tags`) những file nào đó, bạn có thể sử dụng tags. Ví dụ tôi có 2 file:

```yaml
# flowA.yaml
appId: com.example.app
tags: 
  - dev
  - pull-request
```

```yaml
# flowB.yaml
appId: com.example.app
tags: 
  - dev
```

```sh
maestro test --include-tags=dev --exclude-tags=pull-request workspaceFolder/
```

Một số kịch bản sẽ như sau:

* `--include-tags=dev`, flowA và flowB sẽ chạy.
* `--include-tags=dev,pull-request`, cả 2 file sẽ chạy.
* `--exclude-tags=pull-request`, chỉ flowB chạy.
* `--exclude-tags=dev`, không file nào chạy.
* `--include-tags=dev --exclude-tags=pull-request`, chỉ flowB chạy.

Xem thêm [Maestro - Tags](https://maestro.mobile.dev/cli/tags).


## Kiểm thử trên cloud

Ta có thể chạy Maestro Flows trên cloud qua tài liệu [Maestro Cloud Documentation](https://cloud.mobile.dev/).

Maestro Cloud hỗ trợ các nền tảng CI như:

| CI Platform    | Support via CLI | Native Intergation |
|----------------|-----------------|--------------------|
| GitHub Actions | ✅ | ✅ |
| Bitrise        | ✅ | ✅ |
| Bitbucket      | ✅ | ✅ |
| CircleCI       | ✅ | ✅ |
| GitLab CI/CD   | ✅ | 🚧 |
| TravisCI       | ✅ | |
| Jenkins        | ✅ | |
| Tất cả các nền tảng CI khác | ✅ | |


## Videos kiểm thử của Maestro

* [Android contacts flow automation - maestro.mobile.dev](https://maestro.mobile.dev/examples/android-contacts-flow-automation)

[![Android contacts flow automation](https://i.vimeocdn.com/video/1563541340-9e646b93d630d90e1fa93b6c8d140d09a4115de74aafa491a968d0a5ec8252be-d)](https://player.vimeo.com/video/779079600?h=0b1fec8f26)

<br />

* [Facebook signup flow automation - maestro.mobile.dev](https://maestro.mobile.dev/examples/facebook-signup-flow-automation)

[![Facebook signup flow automation](https://i.vimeocdn.com/video/1537181939-8c4e67e47ff72aa7e14642a7fc104a662a457fabc20f6ce076b571d98f497a9d-d)](https://player.vimeo.com/video/765491505?h=21d7adf282)


## Tham khảo thêm

* Maestro: [https://maestro.mobile.dev/](https://maestro.mobile.dev/)
* Maestro Cloud: [https://cloud.mobile.dev/](https://cloud.mobile.dev/)
* Facebook IDB: [https://github.com/facebook/idb](https://github.com/facebook/idb)
* Best tips & tricks for E2E Maestro with React Native: [https://dev.to/retyui/best-tips-tricks-for-e2e-maestro-with-react-native-2kaa](https://dev.to/retyui/best-tips-tricks-for-e2e-maestro-with-react-native-2kaa)
* Test your React Native app with Maestro: [https://dev.to/b42/test-your-react-native-app-with-maestro-5bfj](https://dev.to/b42/test-your-react-native-app-with-maestro-5bfj)
* Streamline Your React Native Testing with Maestro: [Streamline Your React Native Testing with Maestro](https://viniciuspetrachin.medium.com/streamline-your-react-native-testing-with-maestro-bc279586125f)


<p align="center">
  <img style="width:400px" src="https://media.tenor.com/blHCE4Hrc20AAAAd/bravo.gif" alt="https://media.tenor.com/blHCE4Hrc20AAAAd/bravo.gif" />
<p>

Maestro cũng còn rất mới với cộng đồng kiểm thử ứng dụng di động, còn nhiều vấn đề phải chỉnh sửa, nâng cấp. Tuy nhiên, rất xứng đáng để được 1 star trên [Maestro Github](https://github.com/mobile-dev-inc/maestro) cho đội ngũ phát triển.

🎉 🎉 🎉 **Hy vọng bài viết hữu ích với mọi người! Cảm ơn !** 🎉 🎉 🎉


## Đóng góp

Mọi ý kiến cũng như đóng góp luôn được chào đón. Hãy tạo [Issues](https://github.com/tuantvk/rnmaestro/issues) hoặc [Pull requests](https://github.com/tuantvk/rnmaestro/pulls) cho tôi.


## License

[MIT licensed](LICENSE).
