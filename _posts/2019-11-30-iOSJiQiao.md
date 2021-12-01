---
layout: post
title: iOS开发的小技巧
mage: /assets/images/markdown.jpg
headerImage: false
tag:
- IOS技术
blog: true

author: ""
description: ""

---

### collectionView的内容小于其宽高的时候是不能滚动的，设置可以滚动：

```objc
collectionView.alwaysBounceHorizontal = YES;

collectionView.alwaysBounceVertical = YES;
```

### 父视图透明不影响子视图的做法

```objc
self.view.backgroundColor = [[UIColor whiteColor]colorWithAlphaComponent:0.7f];

```

### 判断该图片是否有透明度通道
 
```objc
  - (BOOL)hasAlphaChannel
{
    CGImageAlphaInfo alpha = CGImageGetAlphaInfo(self.CGImage);
    return (alpha == kCGImageAlphaFirst ||
            alpha == kCGImageAlphaLast ||
            alpha == kCGImageAlphaPremultipliedFirst ||
            alpha == kCGImageAlphaPremultipliedLast);
}

```

### 两张图片合成

```objc
+ (UIImage*)mergeImage:(UIImage*)firstImage withImage:(UIImage*)secondImage {
    CGImageRef firstImageRef = firstImage.CGImage;
    CGFloat firstWidth = CGImageGetWidth(firstImageRef);
    CGFloat firstHeight = CGImageGetHeight(firstImageRef);
    CGImageRef secondImageRef = secondImage.CGImage;
    CGFloat secondWidth = CGImageGetWidth(secondImageRef);
    CGFloat secondHeight = CGImageGetHeight(secondImageRef);
    CGSize mergedSize = CGSizeMake(MAX(firstWidth, secondWidth), MAX(firstHeight, secondHeight));
    UIGraphicsBeginImageContext(mergedSize);
    [firstImage drawInRect:CGRectMake(0, 0, firstWidth, firstHeight)];
    [secondImage drawInRect:CGRectMake(0, 0, secondWidth, secondHeight)];
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    return image;
}

```

### 制作水印
```objc
// 画水印
- (void) setImage:(UIImage *)image withWaterMark:(UIImage *)mark inRect:(CGRect)rect
{
    if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 4.0)
    {
        UIGraphicsBeginImageContextWithOptions(self.frame.size, NO, 0.0);
    }
    //原图
    [image drawInRect:self.bounds];
    //水印图
    [mark drawInRect:rect];
    UIImage *newPic = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    self.image = newPic;
}
```
### 移除字符串中的空格和换行
```objc
+ (NSString *)removeSpaceAndNewline:(NSString *)str {
    NSString *temp = [str stringByReplacingOccurrencesOfString:@" " withString:@""];
    temp = [temp stringByReplacingOccurrencesOfString:@"\r" withString:@""];
    temp = [temp stringByReplacingOccurrencesOfString:@"\n" withString:@""];
    return temp;
}

```

### 获取一个视频的第一帧图片
```objc
NSURL *url = [NSURL URLWithString:filepath];
AVURLAsset *asset1 = [[AVURLAsset alloc] initWithURL:url options:nil];
AVAssetImageGenerator *generate1 = [[AVAssetImageGenerator alloc] initWithAsset:asset1];
generate1.appliesPreferredTrackTransform = YES;
NSError *err = NULL;
CMTime time = CMTimeMake(1, 2);
CGImageRef oneRef = [generate1 copyCGImageAtTime:time actualTime:NULL error:&err];
UIImage *one = [[UIImage alloc] initWithCGImage:oneRef];
 return one;

```

### 获取视频的时长

```objc
+ (NSInteger)getVideoTimeByUrlString:(NSString *)urlString {
    NSURL *videoUrl = [NSURL URLWithString:urlString];
    AVURLAsset *avUrl = [AVURLAsset assetWithURL:videoUrl];
    CMTime time = [avUrl duration];
    int seconds = ceil(time.value/time.timescale);
    return seconds;
}

```

### isKindOfClass和isMemberOfClass的区别

```objc
isKindOfClass可以判断某个对象是否属于某个类，或者这个类的子类。
isMemberOfClass更加精准，它只能判断这个对象类型是否为这个类(不能判断子类)

```

###  禁止程序运行时自动锁屏
```objc
[[UIApplication sharedApplication] setIdleTimerDisabled:YES];

```
### 修改textFieldplaceholder字体颜色和大小
```objc
textField.placeholder = @"请输入用户名";  
[textField setValue:[UIColor redColor] forKeyPath:@"_placeholderLabel.textColor"]; 
[textField setValue:[UIFont boldSystemFontOfSize:16] forKeyPath:@"_placeholderLabel.font"];
```
### 禁止textField和textView的复制粘贴菜单
```objc
- (BOOL)canPerformAction:(SEL)action withSender:(id)sender{    
	if ([UIMenuController sharedMenuController]) {      
		[UIMenuController sharedMenuController].menuVisible = NO;  
 		}    
 	return NO;
  }
```

### 移除所有子视图
```objc
[view.subviews makeObjectsPerformSelector:@selector(removeFromSuperview)];
```

### 获取控件在父视图上的坐标尺寸

```objc
UIWindow * window=[[[UIApplication sharedApplication] delegate] window];
CGRect rect=[testView convertRect:testView.bounds toView:window];

```

### 价格有小数且不为0则显示几位小数 没有则显示整数

```objc
- (NSString *)formatPriceWithFloat:(float)price {
    if (fmodf(price, 1)==0) {
        return [NSString stringWithFormat:@"%.0f",price];
    } else if (fmodf(price*10, 1)==0) {//如果有一位小数点
        return [NSString stringWithFormat:@"%.1f",price];
    } else {//如果有两位小数点
        return [NSString stringWithFormat:@"%.2f",price];
    }
}

```

### 统一单例

```objc
+ (instancetype)sharedInstance {
    static id instance;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
    });
    return instance;
}
```