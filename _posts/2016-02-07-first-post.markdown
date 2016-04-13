---
layout: post
title:  "给瀑布流添加header"
date:   2015-03-21 12:36:23
---
瀑布流UICollectionView自从ios6.0更新以来就一直备受瞩目，作为tableview 的兄弟，怎么能没有headerView呢，两个办法，第一个就是最常见的注册头视图
	
	
	@interface ViewController <UICollectionViewDataSource, UICollectionViewDelegate>
	@property (nonatomic, strong) UICollectionView *collectionView;
	static NSString *HELDER = @"MYCELL";
	 - (void)viewDidLoad {
    [super viewDidLoad];
    _collectionView = [[UICollectionView alloc] initWithFrame:CGRectMake(0, 0, [UIScreen mainScreen].bounds.size.width,[UIScreen mainScreen].bounds.size.height) collectionViewLayout:[UICollectionViewFlowLayout new]];
    _collectionView.delegate = self;
    _collectionView.dataSource = self;
    [self.view addSubview:_collectionView];
    [_collectionView registerClass:[CollectionHeaderView class] forSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:HELDER];     //注册头视图

	}
	//通过注册的identifier返回头部视图
	 - (UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath {
    UICollectionReusableView *reusableView = nil;
    if (kind == UICollectionElementKindSectionHeader) {       //头视图
        _headerView = [collectionView dequeueReusableSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:@"Header" forIndexPath:indexPath];
        reusableView = _headerView;
    }
    return reusableView;
	}
	//不设置头部大小，不返回
	 - (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout referenceSizeForHeaderInSection:(NSInteger)section {
    return CGSizeMake(头视图的宽, 头视图的高);
	}

而第二种办法就是创建新的section作为头部视图作为展现，如果你想问我怎么实现瀑布流，提供两个思路，第一个，在cellForItem中重写cell的Frame，第二种直接自定义UICollectionViewFlowLayout，重写布局方法即可。