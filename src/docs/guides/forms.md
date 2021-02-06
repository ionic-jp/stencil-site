---
title: Forms
description: Forms
url: /docs/forms
contributors:
  - jthoms1
---

# Forms

## Basic forms

基本的な形式のコンポーネントの例を次に示します。

```tsx
@Component({
  tag: 'my-name',
  styleUrl: 'my-name.css'
})
export class MyName {

  @State() value: string;

  handleSubmit(e) {
    e.preventDefault()
    console.log(this.value);
    // send data to our backend
  }

  handleChange(event) {
    this.value = event.target.value;
  }

  render() {
    return (
      <form onSubmit={(e) => this.handleSubmit(e)}>
        <label>
          Name:
          <input type="text" value={this.value} onInput={(event) => this.handleChange(event)} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

ここで何が起こっているのかを見てみましょう。 まず、入力の値を状態変数（この場合は `this.value`）にバインドします。 次に、 `onInput`にバインドした`handleChange`メソッドを使用して、状態変数を入力の新しい値に設定します。 `onInput`は、ユーザーが入力に入力するすべてのキーストロークを起動します。


## Advanced forms

より高度な形式のコンポーネントの例を次に示します。

```tsx
@Component({
  tag: 'my-name',
  styleUrl: 'my-name.css'
})
export class MyName {
  selectedReceiverIds = [102, 103];
  @State() value: string;
  @State() selectValue: string;
  @State() secondSelectValue: string;
  @State() avOptions: any[] = [
    { 'id': 101, 'name': 'Mark' },
    { 'id': 102, 'name': 'Smith' }
  ];

  handleSubmit(e) {
    e.preventDefault();
    console.log(this.value);
  }

  handleChange(event) {
    this.value = event.target.value;

    if (event.target.validity.typeMismatch) {
      console.log('this element is not valid')
    }
  }

  handleSelect(event) {
    console.log(event.target.value);
    this.selectValue = event.target.value;
  }

  handleSecondSelect(event) {
    console.log(event.target.value);
    this.secondSelectValue = event.target.value;
  }

  render() {
    return (
      <form onSubmit={(e) => this.handleSubmit(e)}>
        <label>
          Email:
          <input type="email" value={this.value} onInput={(e) => this.handleChange(e)} />
        </label>

        <select onInput={(event) => this.handleSelect(event)}>
          <option value="volvo" selected={this.selectValue === 'volvo'}>Volvo</option>
          <option value="saab" selected={this.selectValue === 'saab'}>Saab</option>
          <option value="mercedes" selected={this.selectValue === 'mercedes'}>Mercedes</option>
          <option value="audi" selected={this.selectValue === 'audi'}>Audi</option>
        </select>

        <select onInput={(event) => this.handleSecondSelect(event)}>
          {this.avOptions.map(recipient => (
            <option value={recipient.id} selected={this.selectedReceiverIds.indexOf(recipient.id) !== -1}>{recipient.name}</option>
          ))}
        </select>

        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```


このフォームは、電子メール入力とともに2つの選択入力があるという点で、もう少し高度です。 また、 `handleChange`メソッドでメール入力の有効性チェックを行います。 `select`要素は、テキスト入力の処理方法と非常によく似た方法で処理します。

妥当性チェックでは、＃usingtheplatformを使用しており、ブラウザーに直接組み込まれている[制約検証API]((https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5/Constraint_validation))を使用して、ユーザーが実際にメールを入力しているかどうかをチェックしています。