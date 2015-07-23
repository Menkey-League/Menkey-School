#Replace Animation

iOS: [https://github.com/MartinRGB/Replace-iOS](https://github.com/MartinRGB/Replace-iOS)  
Android: [https://github.com/race604/FlyRefresh](https://github.com/race604/FlyRefresh)  
Dribble: [https://dribbble.com/shots/2067564-Replace?list=searches&offset=0](https://dribbble.com/shots/2067564-Replace?list=searches&offset=0)  


重点是根据ScrollView的移动位置改变布局

```
@interface ViewController () <UIScrollViewDelegate>

@property (strong, nonatomic) IBOutlet UIScrollView *scview;


- (void)viewDidLoad {
    [super viewDidLoad];
    //imp
    _scview.delegate = self;
    // Do any additional setup after loading the view, typically from a nib.

    //Add Tree & Filter
    [self TreeImagenFilter];
   
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    
    if (scrollView == self.scview ){
    
        [self parallax];
      
    }
}


- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate
{
    CGFloat scrolly = self.scview.contentOffset.y;
    if (scrolly < -100.00) {


        [self Paperplane];
        [self ListPOP];
        
      
        
        }
}

- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView
{
    
    _LNew.alpha = 0;
    

}

-(void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView{
     if (scrollView.contentOffset.y < 0) {
        [scrollView setContentOffset:scrollView.contentOffset animated:NO];
        
        self.startValue = self.scview.contentOffset.y;
        [self popAnimation];
        
     }
    
    
}

```