#Objective-C コーディング規約

#バージョン
v1.0.0

#目次
* はじめに
* 命名規則
* ファイルフォーマット
* プロパティ 
* リテラル
* 関数
* メソッド
* 制御構文
* Blocks
* エラーハンドリング
* Null許容性
* メモリ管理

#はじめに

このコーディング規約は、Objective-Cプログラミングにおいて
保守性、可読性を上げ、成果物の品質を一定水準以上に保つことを目的とする。

#命名規則

##クラス、プロトコル、列挙体、定数
キャメルケースとし、先頭を大文字とすること。(UpperCamelCase)

**理由**
Coding Guidelines for Cocoaに従う。

大文字で3文字のプレフィックスをつけること。

**理由**
Appleが提供するFrameworkは2文字のプレフィックスがつけられており、衝突させないため。

**推奨**

```
クラス
@interface STVTopViewController : UIViewController

プロトコル
@protocol STVSampleProtocol

列挙体
typedef NS_ENUM(NSInteger, STVGlobalConstants)

定数
static NSString *const STVAboutViewControllerCompanyName = @"STV";
extern NSString *const STVAboutViewControllerCompanyName;

```

**非推奨**

```
クラス
@interface topViewController : UIViewController

プロトコル
@protocol sampleProtocol

列挙体
typedef NS_ENUM(NSInteger, globalConstants)

定数
#define kCompanyName @"STV";
static NSString *const aboutViewControllerCompanyName = @"STV";
extern NSString *const aboutViewControllerCompanyName;

```

##メソッド、変数
キャメルケースとし、先頭は小文字とすること。(lowerCamelCase)

**理由**
Coding Guidelines for Cocoaに従う。

**推奨**

```
メソッド
- (instancetype)initWithName:(NSString *)name;
- (BOOL)fileExistsAtPath:(NSString *)path isDirectory:(nullable BOOL *)isDirectory;

変数
NSString *userName;
UIButton *movieStartButton;
```

**非推奨**

```
メソッド
- (instancetype)InitWithName:(NSString *)name;
- (BOOL)FileExistsAtPath:(NSString *)path isDirectory:(nullable BOOL *)isDirectory;

変数
NSString *UserName;
UIButton *MovieStartButton;
```

##ポインタの位置
ポインタ型の*は変数側に寄せること。

**理由**
統一のため。

**推奨**

```
NSString *urlString;
```

**非推奨**

```
NSString* urlString;
```

##カテゴリ拡張のファイル名
「拡張するクラス名」+「+」+「機能名」とすること。

**理由**
どのクラスを拡張したのか分かるため。

**推奨**

```
UIView+Animation.h
```

**非推奨**

```
UIViewAnimation.h
UIViewExtension.h
```

##画像
キャメルケースとし、先頭を大文字とすること。(UpperCamelCase)
一貫した名前を付けること。何のために使われている画像なのか分かる名前を付けること。

**理由**
可読性向上のため。

**例**

```
RefreshBarButtonItem / RefreshBarButtonItem@2x 

RefreshBarButtonItemSelected / RefreshBarButtonItemSelected@2x

ArticleNavigationBarWhite / ArticleNavigationBarWhite@2x 

ArticleNavigationBarBlackSelected / ArticleNavigationBarBlackSelected@2x
```

#ファイルフォーマット

##インポート
以下の順に書くこと。
フレームワーク
↓
ライブラリ

ライブラリが複数ある場合は、グループ分けしてグループ名をコメントすること。
グループ内は、アルファベット順に書くこと。

ヘッダーファイルでの#importは最小限にすること。

\#importの代わりに可能な限り@classを使うこと。

**理由**
循環参照防止のため。
不要なアクセス防止のため。
コンパイル時のパフォーマンス向上のため。

**例**

```
// Frameworks
@import QuartzCore;

// Models
#import "STVUser.h"

// Views
#import "STVButton.h"
#import "STVUserView.h"
```

##Pragma Mark
\#pragma mark -を使用して、メソッドを機能毎にグループ分けすること。
\#pragma mark -の前後は1行改行すること。

メソッドの順序は以下の例を参照し、ヘッダファイル(.h)と実装ファイル(.m)で合わせること。

**理由**
可読性向上のため。

**例**

```

#pragma mark - Lifecycle

- (instancetype)init {}
- (void)dealloc {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)viewDidDisappear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Actions

- (IBAction)tappedLoginButton:(id)sender {}

#pragma mark - Public

- (void)publicMethod {}

#pragma mark - Private

- (void)privateMethod {}

#pragma mark - STVSampleProtocol

#pragma mark - UICollectionViewDataSource

#pragma mark - UICollectionViewDelegate

#pragma mark - UITextFieldDelegate

```

##dealloc
KVO(Key-Value Observing)やNotificationCenter通知を登録した場合は、必ずdeallocで解放すること。

**理由**
意図しないタイミングで通知されるのを防止するため。
クラッシュ防止のため。

**例**

```
-(void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```

##初期化
1クラス内にイニシャライザは原則1つとする。
複数のイニシャライザが必要な場合は、基となる指定イニシャライザを経由して実装すること。

**理由**
可読性向上のため。統一のため。

**推奨**

```
- (instancetype)initWithHeight:(float)height
{
    self = [super init];
    if (self) {
        _height = height;
    }
    return self;
}

- (instancetype)init
{
    self = [super init];
    return [self initWithHeight:0]; // 指定イニシャライザを経由して初期化
}
```

**非推奨**

```
- (instancetype)initWithHeight:(float)height
{
    self = [super init];
    if (self) {
        _height = height;
    }
    return self;
}

- (instancetype)init
{
    self = [super init];
    // 指定イニシャライザを経由せず初期化
    if (self) {
        _height = 0;
    }
    return self;
}
```

##コメント
インタフェース、カテゴリ、プロトコルの宣言にはすべてAppleDoc形式でコメントを記載すること。
コメントには、何のための処理であるか明確に記載すること。

**理由**
可読性向上のため。ドキュメント生成が容易なため。

##改行
コードが長い行は改行をすること。

**理由**
可読性向上のため。

**推奨**

```
self.productsRequest = [[STVProductsRequest alloc]
    initWithProductIdentifiers:productIdentifiers];
```

**非推奨**

```
self.productsRequest = [[STVProductsRequest alloc] initWithProductIdentifiers:productIdentifiers];
```

#プロパティ
##プライベートプロパティ
プライベートなプロパティは、実装ファイル（.m）に宣言すること。

**理由**
不要なアクセスをさせないため。
循環参照を防ぐため。    

**例**

```
@interface STVDetailViewController ()

@property (strong, nonatomic) GADBannerView *googleAdView;

@property (strong, nonatomic) ADBannerView *iAdView;

@property (strong, nonatomic) UIWebView *adXWebView;

@end
```

##ドット記法
プロパティへのアクセスは. を使用すること。
メソッドの呼び出しは. ではなく[ ]を使用すること。

**理由**
プロパティアクセスとメソッドの呼び出しを区別するため。


**推奨**

```
_label.text = @"good";

[UTIApplication sharedApplication].delegate;
```

**非推奨**

```
[_label setText:@"bad"];

UIApplication.sharedApplication.delegate;
```


#リテラル
NSString、NSDictionary、NSArray、NSNumberのイミュータブルなインスタンスを作成するときは、
リテラルを使用し、モダンな書き方をすること。
また、NSArrayやNSDictionaryなど、汎用型をもつクラスに対し、具体的な型を明記すること。

**理由**
可読性向上のため。統一のため。
NSArrayとNSDictionaryのリテラルには、nilを入れるとクラッシュの原因になるため。

**推奨**

```
NSArray<NSString *> *names = @[@"Kohno", @"Kushida", @"Nemoto", @"Hirayama", @"Saito"];

NSDictionary<NSString *, NSString *> *productManagers = @{@"iPhone": @"Kushida", @"iPad": @"Nemoto", @"Web": @"Hirayama"};

NSNumber *shouldUseLiterals = @YES;

NSNumber *buildingStreetNumber = @10018;
```

**非推奨**

```
NSArray *names = [NSArray arrayWithObjects:@"Kohno", @"Kushida", @"Nemoto", @"Hirayama", @"Saito", nil];

NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kushida", @"iPhone", @"Nemoto", @"iPad", @"Hirayama", @"Web", nil];

NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];

NSNumber *buildingStreetNumber = [NSNumber numberWithInteger:10018];
```

#関数
##CGRect関数
CGRectのx、y、widthまたはheightにアクセスする場合、必ずCGGeometry関数を使用すること。
直接CGRectに格納されたデータを読み書きはしないこと。

**理由**
可読性向上のため。 

**推奨**

```
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = CGRectMake(0.0, 0.0, width, height);
```

**非推奨**

```
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
```

#メソッド
##クラスメソッド
クラスメソッドが使われる場合、戻り値の型は'instancetype'とすべきで、'id'としないこと。

**理由**
コンパイラが、正しい戻り値の型を保証するため。

**例**

```
@interface Airplane

+ (instancetype)airplaneWithType:(STVAirplaneType)type;

@end
```

参照　http://nshipster.com/instancetype/

##シングルトン
シングルトンオブジェクトは、スレッドセーフなパターンを使用すること。

**理由**
共有インスタンスを生成するため。

**例**

```
+ (instancetype)sharedInstance {
    static id sharedInstance = nil;

    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [[[self class] alloc] init];
    });

    return sharedInstance;
}
```

#制御構文

##早期リターン
条件文を記述する場合、可能な限り早期リターンをすること。

**理由**
ネストを深くしないため。

**推奨**

```
- (void)someMethod {
  if (!isSuccess) {
    return;
  }

  // 処理
}
```

**非推奨**

```
- (void)someMethod {
  if (isSuccess) {
    // 処理
  } else {
    return;
  }
}
```

##case文
case文で複数行の処理を{ }で囲う必要がある場合、case文と同じ行から開いてbreakの次の行で閉じること。

**理由**
可読性向上のため。

**例**

```

typedef NS_ENUM(NSInteger, STVScreenType) {

    STVScreenTypeMain,
    STVScreenTypeDetail,
    STVScreenTypeInformation
    STVScreenTypeSetting
};

switch (screenType) {

    case STVScreenTypeMain:
        // 処理
        break;

    case STVScreenTypeDetail: {
        // 
        // 複数行の処理
        // 
        break;
    }

    case STVScreenTypeInformation:
    case STVScreenTypeSetting:
        // 処理
        break;
}
```

##BOOL値
オブジェクトがnilかどうか評価したい時、if文の条件式内で == nilとしないこと。
また、nilじゃないかどうかを評価したい場合も!= nilともしないこと。

BOOL値をYESと比較しないこと。

**理由**
YESは1だが、Cbjective-CのBOOL値は、8ビットlongのCHAR型で定義されているから。

- **オブジェクトの場合**

**推奨**

```
if (!someObject) {

}
```

**非推奨**

```
if (someObject == nil) {

}
```


- **BOOL値の場合**

**推奨**

```
if (isAwesome)

if (![someObject boolValue])
```

**非推奨**

```
if ([someObject boolValue] == NO)

if (isAwesome == YES) // Never do this.
```

##三項演算子
三項演算子を使用する場合、一文の中に三項演算子を複数記述しないこと。

**理由**
可読性向上のため。

**推奨**

```
result = a > b ? x : y;
```

**非推奨**

```  
result = a > b ? x = c > d ? c : d : y;
```

#Blocks
引数としてBlocksを使用するメソッドにおいて、外部引数名で改行する場合は { の前で改行して
インデントが深くならないようにすること。

**理由**
可読性向上のため。

**推奨**

```
[sampleClient GET:path
       parameters:parameters
          success:^(NSURLSessionDataTask *task, id responseObject)
{
    if (responseObject) {
        NSLog(@“response:%@”, responseObject);
    }
}];
```        

**非推奨**

```
[sampleClient GET:path
       parameters:parameters
          success:^(NSURLSessionDataTask *task, id responseObject) {
              if (responseObject) {
                  NSLog(@“response:%@”, responseObject);
              }
          }
];
```

#エラーハンドリング
NSErrorでエラーハンドリングをする場合、error変数の宣言時にnilで初期化すること。

**理由**
error変数をnilで初期化しないとEXC_BAD_ACCESSが発生し、正しくエラーハンドリングをできなくなるため。

**推奨**

```
NSError *error = nil;

[self trySampleWithError:&error];

if (error) {
    // エラー時の処理

}
```

**非推奨**

```
NSError *error;

[self trySampleWithError:&error];

if (error) {
    // エラー時の処理

}
```

#Null許容性
プロパティ、メソッドのパラメータ、戻り値を記載する際、nullable, nonnull を指定し、null許容性を明らかにすること。

**理由**
swift連携時の安全性向上のため。

#メモリ管理
ARCを使用すること。
ARC対応のライブラリ・フレイムワークを使用すること。
不要となったオブジェクトは、解放すること。
補足：メモリリークの調査にはINSTRUMENTが有効。

**理由**
メモリリーク防止のため。

#参考スタイルガイド
NYTimes
https://github.com/NYTimes/objective-c-style-guide#xcode-project

raywenderlich
https://github.com/raywenderlich/objective-c-style-guide#xcode-project

github
https://github.com/github/objective-c-style-guide

Robots & Pencils
https://github.com/RobotsAndPencils/objective-c-style-guide#singletons

soffes
https://gist.github.com/soffes/812796

Coding Guidelines for Cocoa
https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines
