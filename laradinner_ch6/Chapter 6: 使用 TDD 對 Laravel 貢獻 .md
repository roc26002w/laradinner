# Chapter 6: 使用 TDD 對 Laravel 貢獻 

在開源社群有一個基本規則 ，特別是在大型專案上，這規則是節省時間的基本知識 :
如果 pull request 沒有任何的測試，那請不要發 pull request 。

一但一個專案達到了一定的人氣時，那 pull requests 一定會遵守。
重要的是要記住，這些項目背後的大多數主要開發人員都是在業餘時間進行管理。
您的貢獻伴隨著測試可以節省很多的幫助：
1.提供的代碼究竟做了什麼？測試將會是一個例子。 
2.測試將證明您的代碼正常工作，且不會破壞現有的功能。 
Travis CI（本書稍後介紹）等自動化工具使此功能更加有用。

*Warning: 請注意，本章假設您對Git有基本的了解。如果沒有，現在將是學習的最佳時機。*

### Creating a Proposal

在對 Laravel 貢獻之前, 第一步是要建一個描述意圖的提案。
此意思能減免你花幾個小時寫了新 code , 而泰勒並不覺得它屬於核心的代碼。
要提交提案，請訪問存儲庫並創建一個以[Proposal]開頭的描述性標題的新票。
這裡有一個例子 : 
```
	[Proposal] Add selectMonth and selectYear to FormBuilder
	https://github.com/laravel/framework/issues/1502
```
提交請求之前先創建提案，而泰勒將給你一個豎起大拇指或向下的大拇指，如果反應良好，那麼你可以自由的改善它

*Tip: 如果專案的所有者拒絕或修改您的請求，不要感到不舒服。因為這是他們的寶貝，您的貢獻是非常重要的，因為您正在幫助他們將自己的應用完成到更好。*

但是，對於簡單的錯誤修復，您可以省略以[Proposal]開頭。

### Your Local Copy

對 Laravel 做出貢獻並不是找到錯誤時把應該更正的地方 email 或 tweeting 給泰勒. 
但這會導致訊息被忽略，所以使用GitHub來管理專案，要對 Laravel 做出貢獻第一步就是 Clone Laravel 專案。

`git clone https://github.com/laravel/framework.git`

*Tip: 標準的Laravel框架 Clone 時不會包括PHPUnit的測試包下來。*

下一步, clone 完後下指令案裝

`composer install --dev`

此時，您應該同時擁有一個測試和src目錄。
這是測試的最佳做法，測試的資料夾結構跟src的資料夾結構會是相互關聯的。

### Branching

這裡有一個基本的規則，就是為每個錯誤修復或功能創建一個新的Git分支。
這允許讓主分支保持不變，開發或錯誤修復都能獨立於主分支
大多數開發團隊都有自己的分支命名規則 : 

`git checkout -b feature/bookmarking`
`git checkout -b bug/messages-posted-twice`

此命令將創建一個新的分支並在此分支上。

### Coding Guidelines

成功的 pull request 重要的部分是遵守專案編碼規則。
下面有幾個Laravel特定的範例 : 

```
1  //WRONG2  foreach($namesas as $name) {3 
4  } 
56  //CORRECT7  foreach ($namesas as $name)8  { 
910 }
```

大括號應該在新的一行上，以及foreach之後應該要有的空白。

```
1  //WRONG2  if (!$this->isOld())3  { 
4 
5  } 
67  //CORRECT8  if ( ! $this->isOld())9  {1011 }
```

要注意，操作符的空白。

```
1   //WRONG2   <?php34   namespace Illuminate\Html; 
56   class Foo 
7   {89   }1011  //CORRECT12  <?php namespace Illuminate\Html;13 14  class Foo { 
1516  }
```

命名空間聲明應該與php標籤的同一行上


```
1  //CORRECT2  return ['foo' => 'bar'];34  //Option5  return array('foo' => 'bar');
```

Laravel 5.5 必須在 PHP >= 7.0.0 這裡就看個人

### Hands On

假設已經遵循了前面的每個步驟，讓我們使用TDD來添加一些功能。
Assuming that each of the prior steps have been followed, let’s use TDD to add a bit of functionality.

### Agenda

如果 Laravel 的 FormBuilder 類別提供了幾個方法 selectYear 和 selectMonth，而不是一直的創建一個`<select>`元素和所有適用的`<option>`，我們可以調用一個方法，它會自動的創建，那麼這不是便利的嗎？我們如何實現這種功能？當然！我們開始一個測試。


### selectYear

FormBuilder 類的測試位於 `tests/Html/FormBuilderTest.php` 中。
在這個文件中，我們添加一個新的測試來描述最終目標。

```

1  //tests/Html/FormBuilderTest.php23  public function testFormSelectYear() 
4  {5      $select = $this->formBuilder->selectYear('year', 2000, 2001);67      $this->assertEquals(8        '<select name="year"><option value="2000">2000</option><option value="2001">2001</option></select>',10       $select
11      ); 
12  }```

很簡單吧，當調用 selectYear 方法（使用它的相應的Facade，這將是Form :: selectYear（）），並且一個名稱和範圍都作為參數傳遞，並返回期望的HTML片段。

*從技術上講，如果遵循TDD的規則，我們應該只寫出足夠的測試。但本章將使用幾種快捷的方式*

運行測試 : 

`phpunit tests/Html`


我們將被告知 selectYear 方法不存在。就像是個小遊戲 : 
1.運行測試，看到失敗
2.編寫代碼，通過測試。 
3.重複測試。 
FormBuilder 類位於 `src/Illuminate/Html/FormBuilder.php` 中。
在此類別添加新的方法如下 :

```
1   //src/Illuminate/Html/FormBuilder.php 
23   public function selectYear()4   {5 
6   }
```

現在，PHPUnit 測試出現：
```
1. FormBuilderTest::testFormSelectYear2. PHPUnit_Framework_Exception: 
Argument #2 of PHPUnit_Framework_Assert::assertContains() must be a array, iterator or string;
```

加入程式碼修正 : 

```
  1 public function selectYear() 
  2 {  3   return array();  4 }
```

現在，PHPUnit 測試出現：
```1 FormBuilderTest::testFormSelectYear2 Failed asserting that an array contains'<select name="year"><option value="2000">2000</option><option value="2001">2001</option></select>'.
```
此時，我們可以開始編寫必要的代碼 : 

```
1  public function selectYear($name,$begin,$end,$selected=null,$options=array())3  {4     $range = range($begin, $end);56     // We want the value for each option to7     // be the same as the text content8     $range = array_combine($range, $range);910    return $this->select($name, $range, $selected, $options);11  }
```

綠燈！因為 FormBuilder 類已經提供了一個`select`方法，
所以不用重新再寫一次，我們只需要創建適用的參數，然後將其傳遞給 select（）。但是，此代碼不是特定於年份。
它可以應用於任何範圍，所以最好將此代碼提取成自己的方法，selectRange，然後直接從 selectYear 呼叫。
因為我們有通過測試，我們可以自由地重構。

```
1    public function selectYear()
2    {
3        return call_user_func_array(
4            array($this, 'selectRange'),
5            func_get_args()
6        );
7     }
8
9     public function selectRange($name,$begin,$end,$selected=null,$options=arr\10    ay())11    {12        $range = range($begin, $end);1314        // We want the value for each option to15        // be the same as the text content16        $range = array_combine($range, $range);1718        return $this->select($name, $range, $selected, $options);19 } 
```
運行測試我們會知道我們的重構是否成功，如果失敗，我們會看到紅燈！但出現的是綠燈。
我們應該添加更多的測試 - 來驗證應用所選的值和選項是否正常的工作。


```
1    public function testFormSelectYear() 
2    {3        $select1 = $this->formBuilder->selectYear('year', 2000, 2001);
4        $select2 = $this->formBuilder->selectYear('year', 2000, 2001, null, array('id'=>'foo'));
5        $select3 = $this->formBuilder->selectYear('year', 2000, 2001, '2000');
6 
7        $this->assertEquals('<select name="year"><option value="2000">2000</option><option value="2001">2001</option></select>',$select1);
8        $this->assertEquals('<select id="foo" name="year"><option value="2000">2000</option><option value="2001">2001</option></select>',$select2);
9        $this->assertEquals('<select name="year"><option value="2000" selected="selected">2000</option><option value="2001">2001</option></select>',$select3);10    } ```

請注意，測試的三元素，`紅燈`、`綠燈`、`重構`!

### selectMonth

`selectYear`和`selectRange`已經完成了，那添加`selectMonth`就不應該太難了。
可以從幾個測試開始：

```
1    public function testFormSelectMonth()
2    {
3        $month1 = $this->formBuilder->selectMonth('month');
4        $month2 = $this->formBuilder->selectMonth('month', '1');
5        $month3 = $this->formBuilder->selectMonth('month', null, array('id' => 'foo');6        $this->assertContains('<select name="month"><option value="1">January</option>',$month1);7        $this->assertContains('<select name="month"><option value="1" selected="selected">January</option>',$month2);
8        $this->assertContains('<select id="foo" name="month"><option value="1">January</option>',$month3);9     }
```

產生代碼讓它們綠燈：

```
1    public function selectMonth($name,$selected=null,$options=array()) 
2    {3        $months = array();4        foreach (range(1, 12) as $month)5        {6            $months[$month] = strftime('%B', mktime(0, 0, 0, $month)); 
7        }8        9        return $this->select($name, $months, $selected, $options); 
10    }
```

正如文件所說，"這可以不是"
As Doc would say, “it couldn’t be simpler.”

*strftime : 根據區域設置來格式化本地日期和時間*


### Summary

GitHub的貢獻新手經常因為被拒絕 pull request 而產生恐懼。但不要因此而害怕，因為這是原創者的寶貝，可能只是原創者跟你的角度不同而拒絕你。
*https://github.com/laravel/framework/commit/308b71034a3543b447e27c12bb613ad1f5621620*

