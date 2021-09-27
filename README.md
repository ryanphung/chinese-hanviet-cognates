# Overview

This Jupyter Notebook aims to answer this question:

```
How many modern Chinese words have a modern Vietnamese Han Viet cognate? And what are they?
```

# Getting Started

You'll need to have [python3](https://www.python.org/downloads/) installed, and some basic knowledge of how to run a [Jupyter notebook](https://jupyter.org/).

Create a virtual environment. This step is optional, but recommended as all requirements will be installed locally and won't interfere with your other python projects.
```
python3 -m venv env
source env/bin/activate
```

Install project requirements:
```
pip install -r requirements.txt
```

Run Jupyterlab:
```
jupyter lab
```

Run the notebook `chinese-hanviet.ipynb`

# Background

*Sep-2021*

I have been learning Chinese over the past few months; and is currently looking at building up my Chinese vocabulary. As a Vietnamese speaker, I have always had a feeling that many Chinese words I stumble upon are similar to Vietnamese words.

Indeed that is the case, because many Vietnamese words were borrowed from Chinese.

A majority of these loanwords belong to a layer called [Han Viet](https://en.wikipedia.org/wiki/Sino-Vietnamese_vocabulary) that were borrowed from Middle Chinese. In linguistics, these words and their Modern Chinese counterparts are called [cognates](https://en.wikipedia.org/wiki/Cognate).

There is also an **Old Sino-Vietnamese** layer consisting of a few hundred words borrowed from earlier Chinese. Most of these words have existed for long enough and have had their sounds diverged enough from their Chinese counterparts that they are considered by Vietnamese as native; and hence I will not consider them in this project. There is also a layer of more recently borrowed words from Modern Chinese, which I believe is small enough that we can safely ignore in view of our practical purpose.

Anyway, the layer of Han Viet itself is quite extensive. Its extensive existence makes it easier for a Vietnamese to learn Chinese, compared to someone whose native langauge is English, for example.

But how easy?

I set out to answer this question. More specifically, this:

```
How many modern Chinese words have a modern Vietnamese Han Viet cognate? And what are they?
```

I also want to have a nice list of these words, so that perhaps I could focus on studying first, to be more efficient with my vocabulary building.

* **Why Modern Chinese?** Because it's the language I'm interested in learning (not Classical Chinese).

* **Why Modern Vietnamese?** Because that's my native language. It means that as much as I can, I shold ignore Han Viet cognates that are not familiar to a typical modern Vietnamese (like myself).

* **Why Han Viet?** As explained above, we are going to ignore the Old Sino-Vietnamese layer and the layer of words borrowed from modern Chinese.

# Methodology

### 1. Starting with Chinese phrases

First, we need a Chinese words frequency list. I chose the [Leeds database](http://corpus.leeds.ac.uk/frqc/internet-zh.num); but I've also included the [BLCU database](https://www.plecoforums.com/threads/word-frequency-list-based-on-a-15-billion-character-corpus-bcc-blcu-chinese-corpus.5859/) which can be run against with some minor tweaks in the code. Either of them should be good for our purpose.

Note that both lists are based on Simplified Chinese.

### 2. Translating to Han Viet sounds

Second, we need to find the corresponding Han Viet sounds for each Chinese words. This is done by looking up the [Thiều Chữu dictionary](https://vi.wikipedia.org/wiki/Thi%E1%BB%81u_Ch%E1%BB%ADu).

The Thiều Chữu dictionary is character-dictionary, not word. It means that for each word, we need to find out the sound for each character and combine them together. For example:
```
我 = ngã
们 = môn
hence: 我们 = ngã môn
```

Sometimes, each character has many sounds. In that case, all combinations are produced. For example:
```
利害 = lợi hại, lợi hạt
```

The Thieu Chuu dictionary has both simplified Chinese and traditional Chinese characters. However it is not always consistent. For example:
```
过 = quá
but
過 = qua, quá
```
To ensure that we don't miss out on some sound because, we also produce the Han Viet sounds based on traditional version of the words.

This ensure we produce all possible Han Viet sounds for words like this:
```
通过 = thông qua, thông quá
```

But it also produce some unintended Han Viet sounds. For example:
```
有着 = hữu trứ, dựu khán, hữu khán, dựu trứ
```
But don't worry, we will deal with these in the next step.

There is also a problem with the Thiều Chữu dictionary where some character only exist as a non-standard variant, for example `硏` instead of `研` (note the very minor difference), or `伱` instead of `你`. As a result, we missed out picking up cognates such as `研究 / nghiên cứu`. To mitigate this problem, we supplement the Thiều Chữu list with a list of sound obtained from the Chinese-Vietnamese translation community, in the file [phienam.txt](inputs/phienam.txt).

### 3. Which ones are legitimate Han Viet words?

Third, we need to find out which one of these Han Viet sounds are actually valid modern Han Viet words.

To do this, we rely on the file [vietphrases.txt](inputs/vietphrases.txt) produced by a Vietnamese translation community (`source missing`). This file contains the translation of common Chinese phrases. Let's take a look at a few examples:

```
批准=phê chuẩn/chuẩn y/chuẩn phê/duyệt y/bằng lòng/thông qua
黑脸=mặt đen
```
The first word, ```批准``` is a cognate with the Han Viet word ```phê chuẩn```.

The second word ```黑脸```, even though producing three Han Viet sound ```hắc kiểm, hắc liễm, hắc kiểm```, none of these is a valid Han Viet word. That's why the Vietnamese translation only include the native Vietnamese word ```mặt đen```.

That is the trick we're going to do here: we're going to recognise a word as a valid Han Viet word if its Han Viet sound exist in the translation.

This is nowhere near fool-proofed, and it depends on the quality of this file `vietphrases.txt`. But our assumption here is that if a word is good enough to be included in the Vietnamese translation by the translation community, we're going to assume that it is a word recognisable by a typical Vietnamese.

In addition, some Vietnamese words have a few spelling variants. The most common one is replacing `y` with `i`, or vice-versa. For example: `công ti` and `công ty` are the same word. In this step if we only do a naive string comparison, we'll end up missing out these words in the final result. Thus we normalised the words first to a single form: `công ti`, `mĩ thuật`, etc. before comparing them.

### 4. Weed out the really uncommon words

Finally, we realised that a lot of resulting words, like like `一些 / nhất ta`, are still either not real Vietnamese words, or are uncommon enough that a typical Vietnamese speaker might not be familiar with. In this step, we will weed out these words by running the list against a colloquial Vietnamese [word frequency list](https://github.com/garfieldnate/vi_experiments/tree/master/opensubs_word_list) collected from Open Subtitles. Words that have a frequency of 1, or do not exist in that list, are removed from the final results.

# Results

### List of Han Viet cognates

The full list of 4,543 words can be downloaded from [chinese-hanviet-cognates.tsv](outputs/chinese-hanviet-cognates.tsv). An example of the first 100 entries can be found at the end of this document.

It could be useful for a Vietnamese learning Chinese looking for a list of easy to learn words.

The full list of 24,414 non-cognates can also be downloaded from [chinese-hanviet-non-cognates.tsv](outputs/chinese-hanviet-non-cognates.tsv). This is also an interesting list to look at because while the first list sounds familiar to a Vietnamese speaker, this second list almost feels like it's an entirely different language.

### Chart

I also plotted the first column against the second column:

![img](./outputs/chart.jpg)

I only consider the top 10,000 Chinese words since we're only considering the common Vietnamese words. The graph is expected to flatten out as we go into the area of uncommon words.

As can be seen from this, around 1/2 to 1/4 of the most frequently used modern Chinese phrases have a Han Viet cognate.

### Limitations

1. This is perhaps the biggest limitation: as can be seen from the example list above, words like `大家` is indicated as having its cognate as `đại gia` (influential family). While this is technically true, this meaning is not as frequently used as the common meaning `mọi người` (everybody); certainly not frequently enough to warrant it to be in the top 36th spot. This is a problem with frequency list - there is no distinction between different meanings of the same word. Therefore, if you are a language learner, you should use this list with care and bear in mind that quite often these Chinese words don't have the same meaning as the primary meaning of the Han Viet words.

2. The project is done based on a frequency list, which means that it is based on Chinese phrases rather than words (as opposed to using a dictionary). However I believe that for the purpose of language learning, this is more useful.

3. I decided to remove single-syllabic words from the result because I think most single-syllabic cognates are not well-understood by native Vietnamese speakers. For example words like `骑 / kỵ` may be marked as a valid Han Viet word; but many people will have no idea what it means, unless it's put in the context of a multi-syllabic word like `骑士 / kỵ sĩ`. Again, this is a practical decision, motivated by my language learning need.

4. As mentioned above, I ignored the Old Sino-Vietnamese layers. This is because on one hand, identifying them is HARD, and on the other hand, these words have diverged enough from their modern Chinese cognates that it's not too useful for language learners to know them.

# Further Works

* More analysis on single-syllabic cognates.

* Replace Thiều Chữu dictionary with a more comprehensive way to produce Han Viet sound. Perhaps [Thi Vien](https://hvdic.thivien.net/)?

* Manual vetting of the results.

# Credits

* [Leeds database](http://corpus.leeds.ac.uk/frqc/internet-zh.num)
* [BLCU database](https://www.plecoforums.com/threads/word-frequency-list-based-on-a-15-billion-character-corpus-bcc-blcu-chinese-corpus.5859/)
* Thiều Chữu dictionary
* **vietphrases.txt** and **phienam.txt** provided by Chinese-Vietnamese translation community
* [OpenSubtitles-based Vietnamese Frequency Dictionary](https://github.com/garfieldnate/vi_experiments/tree/master/opensubs_word_list)
* Thanks to [anh Tran Chi Hieu](https://www.facebook.com/504631273/) for contributing valuable advices and data

All data are owned by their respective owners. As much as I can, I try to cite the source of the data. If you are the owner of the data and would like your data to be removed, please email me at ryan.phung@gmail.com.

# Appendix

### Top 100 cognates:

|index|chinese_rank|frequency|word|traditional|pinyin   |matched      |meaning                                                                                                                                                                                                    |
|-----|------------|---------|----|-----------|---------|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|1    |5           |1332.15  |可以  |可以         |kěyǐ     |khả dĩ       |có thể/khả dĩ/có khả năng/có năng lực/cho phép/được phép/tốt/giỏi/hay/lợi hại/ghê hồn/cừ khôi/ghê gớm                                                                                                      |
|2    |8           |1106.78  |中国  |中國         |zhōngguó |trung quốc   |Trung Quốc/Trung Hoa Trung Quốc/China/nước cộng hoà nhân dân Trung Hoa                                                                                                                                     |
|3    |13          |916.48   |现在  |現在         |xiànzài  |hiện tại     |hiện tại/hiện nay/bây giờ                                                                                                                                                                                  |
|4    |16          |831.2    |时间  |時間         |shíjiān  |thời gian    |thời gian/giờ/khoảng thời gian/thời điểm                                                                                                                                                                   |
|5    |18          |775.81   |问题  |問題         |wèntí    |vấn đề       |vấn đề/câu hỏi/đề hỏi/quan trọng/mấu chốt/chuyện/trở ngại/trắc trở                                                                                                                                         |
|6    |20          |734.66   |工作  |工作         |gōngzuò  |công tác     |công tác/làm việc/việc làm/nghề nghiệp/công việc/nhiệm vụ/nghiệp vụ                                                                                                                                        |
|7    |23          |621.34   |学生  |學生         |xuéshēng |học sinh     |đệ tử/học sinh/học trò/con trai                                                                                                                                                                            |
|8    |24          |601.9    |所以  |所以         |suǒyǐ    |sở dĩ        |cho nên/sở dĩ/đó là lí do mà/nguyên cớ/vì sao/nguyên do                                                                                                                                                    |
|9    |31          |568.4    |生活  |生活         |shēnghuó |sinh hoạt    |cuộc sống/sinh hoạt/đời sống/sinh tồn/tồn tại/mức sống/việc/công việc                                                                                                                                      |
|10   |36          |523.07   |大家  |大家         |dàjiā    |đại gia      |mọi người/đại gia/chuyên gia/người nổi tiếng/bậc thầy/họ lớn/thế gia vọng tộc                                                                                                                              |
|11   |37          |517.6    |可能  |可能         |kěnéng   |khả năng     |có thể/khả năng/khả thi/thực hiện được/làm được/có lẽ/hoặc giả/chắc là                                                                                                                                     |
|12   |47          |470.89   |东西  |東西         |dōngxī   |đông tây     |đồ vật này nọ/đông tây/phía đông và phía tây/từ đông sang tây/đồ/vật/thứ/đồ đạc/đồ vật đồ                                                                                                                  |
|13   |48          |467.23   |北京  |北京         |běijīng  |bắc kinh     |Bắc Kinh/Beijing                                                                                                                                                                                           |
|14   |52          |439.7    |地方  |地方         |dìfāng   |địa phương   |địa phương/chỗ/bản xứ/bản địa/nơi ấy/chốn ấy/nơi/vùng/miền bộ phận/phần                                                                                                                                    |
|15   |54          |435.13   |非常  |非常         |fēicháng |phi thường   |phi thường/đặc biệt/bất thường/không bình thường/rất/vô cùng/cực kỳ/hết sức                                                                                                                                |
|16   |55          |434.38   |发现  |發現         |fāxiàn   |phát hiện    |phát hiện/tìm ra/tìm tòi/phát giác/cảm thấy                                                                                                                                                                |
|17   |56          |428.48   |不过  |不過         |bùguò    |bất quá      |bất quá/cực kỳ/hết mức/nhất trên đời/hơn hết/vừa mới/vừa/chỉ/chẳng qua/chỉ vì/chỉ có/nhưng/nhưng mà/có điều là/song/chỉ có điều                                                                            |
|18   |60          |417.63   |世界  |世界         |shìjiè   |thế giới     |thế giới/vũ trụ/trái đất/thời buổi                                                                                                                                                                         |
|19   |62          |410.19   |一定  |一定         |yīdìng   |nhất định    |nhất định/chính xác/quy định/tất nhiên/cần phải/chắc chắn/riêng                                                                                                                                            |
|20   |64          |405.86   |进行  |進行         |jìnxíng  |tiến hành    |tiến hành/làm/tiến lên/tiến tới/tiến lên phía trước                                                                                                                                                        |
|21   |66          |402.71   |公司  |公司         |gōngsī   |công ty      |công ty/hãng                                                                                                                                                                                               |
|22   |69          |391.68   |朋友  |朋友         |péngyǒu  |bằng hữu     |bằng hữu/bạn bè/bạn/người yêu                                                                                                                                                                              |
|23   |70          |389.11   |国家  |國家         |guójiā   |quốc gia     |quốc gia/nhà nước/đất nước/lãnh thổ                                                                                                                                                                        |
|24   |72          |387.14   |所有  |所有         |suǒyǒu   |sở hữu       |tất cả/sở hữu/vật sở hữu/hết thảy/toàn bộ                                                                                                                                                                  |
|25   |73          |382.93   |个人  |個人         |gèrén    |cá nhân      |cá nhân/người/riêng tôi/cá nhân tôi                                                                                                                                                                        |
|26   |74          |377.52   |发展  |發展         |fāzhǎn   |phát triển   |phát triển/mở rộng/khuếch trương                                                                                                                                                                           |
|27   |75          |374.82   |最后  |最後         |zùihòu   |tối hậu      |cuối cùng/tối hậu/sau cùng                                                                                                                                                                                 |
|28   |79          |365.5    |电话  |電話         |diànhuà  |điện thoại   |điện thoại/máy điện thoại/dây nói                                                                                                                                                                          |
|29   |82          |363.35   |学习  |學習         |xuéxí    |học tập      |học tập/học                                                                                                                                                                                                |
|30   |86          |347.8    |当时  |當時         |dāngshí  |đương thời   |lúc ấy/lúc đó/khi đó/đương thời/lập tức/liền/ngay lúc đó/ngay lúc ấy                                                                                                                                       |
|31   |87          |344.15   |情况  |情況         |qíngkuàng|tình huống   |tình huống/tình hình                                                                                                                                                                                       |
|32   |89          |343.32   |社会  |社會         |shèhùi   |xã hội       |xã hội/hình thái xã hội                                                                                                                                                                                    |
|33   |91          |339.89   |希望  |希望         |xīwàng   |hy vọng      |hy vọng/mong muốn/ước ao/mong/ý muốn/ước muốn/nguyện vọng/niềm hi vọng/mong ngóng                                                                                                                          |
|34   |93          |331.77   |感觉  |感覺         |gǎnjué   |cảm giác     |cảm giác/cảm thấy/cho rằng                                                                                                                                                                                 |
|35   |94          |331.4    |记者  |記者         |jìzhě    |ký giả       |phóng viên/ký giả/nhà báo                                                                                                                                                                                  |
|36   |95          |331.28   |女人  |女人         |nv̌rén   |nữ nhân      |nữ nhân/phụ nữ/đàn bà/vợ                                                                                                                                                                                   |
|37   |96          |326.67   |第二  |第二         |dìèr     |đệ nhị       |đệ nhị/thứ hai                                                                                                                                                                                             |
|38   |97          |325.21   |其实  |其實         |qíshí    |kỳ thật      |kỳ thật/kỳ thực/thực ra                                                                                                                                                                                    |
|39   |98          |325.1    |事情  |事情         |shìqíng  |sự tình      |sự tình/chuyện/sự việc                                                                                                                                                                                     |
|40   |104         |303.44   |过去  |過去         |guòqù    |quá khứ      |quá khứ/đi tới/đã qua/trước đây/đi qua/qua/mất/chết/tạ thế                                                                                                                                                 |
|41   |106         |299.29   |教育  |教育         |jiàoyù   |giáo dục     |giáo dục/đào tạo/dạy dỗ/dạy bảo/chỉ dẫn/chỉ thị/dạy                                                                                                                                                        |
|42   |108         |296.8    |文化  |文化         |wénhuà   |văn hóa      |văn hóa/văn hoá                                                                                                                                                                                            |
|43   |109         |293.61   |特别  |特別         |tèbié    |đặc biệt     |đặc biệt/vô cùng/rất/riêng biệt/chuyên biệt/càng/nhất là                                                                                                                                                   |
|44   |111         |289.96   |当然  |當然         |dāngrán  |đương nhiên  |đương nhiên/nên như thế/phải thế/tất nhiên/dĩ nhiên                                                                                                                                                        |
|45   |112         |289.89   |要求  |要求         |yàoqíu   |yêu cầu      |yêu cầu/đòi hỏi/hi vọng/nguyện vọng                                                                                                                                                                        |
|46   |115         |283.2    |不同  |不同         |bùtóng   |bất đồng     |bất đồng/khác nhau/khác biệt                                                                                                                                                                               |
|47   |116         |280.62   |出现  |出現         |chūxiàn  |xuất hiện    |xuất hiện/nảy sinh/ló ra/nổi lên/hiện ra                                                                                                                                                                   |
|48   |117         |279.59   |重要  |重要         |zhòngyào |trọng yếu    |trọng yếu/quan trọng                                                                                                                                                                                       |
|49   |118         |279.48   |通过  |通過         |tōngguò  |thông qua    |thông qua/đi qua/qua                                                                                                                                                                                       |
|50   |119         |279.16   |市场  |市場         |shìcháng |thị trường   |thị trường/chợ                                                                                                                                                                                             |
|51   |120         |278.73   |日本  |日本         |rìběn    |nhật bản     |Nhật Bổn/Nhật bản                                                                                                                                                                                          |
|52   |124         |273.66   |英语  |英語         |yīngyǔ   |anh ngữ      |tiếng Anh/Anh ngữ/Anh văn                                                                                                                                                                                  |
|53   |125         |271.66   |一切  |一切         |yīqiē    |nhất thiết   |hết thảy/tất cả/mọi/mọi thứ/toàn bộ/nhất thiết/hết thẩy                                                                                                                                                    |
|54   |126         |269.91   |活动  |活動         |huódòng  |hoạt động    |hoạt động/chuyển động/vận động/theo mục đích/vì mục đích nào đó/hành động có mục đích/lay động/lung lay/không ổn định/đung đưa/lắc lư/linh hoạt/di động/không cố định/sinh hoạt/chạy chọt/đút lót/mua chuộc|
|55   |128         |268.61   |大学  |大學         |dàxué    |đại học      |đại học                                                                                                                                                                                                    |
|56   |129         |267.09   |主要  |主要         |zhǔyào   |chủ yếu      |chủ yếu/chính                                                                                                                                                                                              |
|57   |130         |266.28   |经济  |經濟         |jīngjì   |kinh tế      |kinh tế/lợi hại/mức sống/đời sống/tiết kiệm/đỡ tốn kém/hạn chế/trị nước/trị quốc                                                                                                                           |
|58   |131         |265.54   |同时  |同時         |tóngshí  |đồng thời    |đồng thời/song song/trong khi/cùng lúc/hơn nữa                                                                                                                                                             |
|59   |132         |265.42   |研究  |研究         |yánjīu   |nghiên cứu   |nghiên cứu/tìm tòi học hỏi                                                                                                                                                                                 |
|60   |133         |264.11   |关系  |關係         |guānxì   |quan hệ      |quan hệ/liên quan/liên quan đến/quan hệ đến/quan trọng/hệ trọng/nguyên nhân/điều kiện/giấy chứng nhận/liên hệ/quan hệ tới                                                                                  |
|61   |138         |257.68   |作者  |作者         |zuòzhě   |tác giả      |tác giả/tác gia                                                                                                                                                                                            |
|62   |140         |255.71   |发生  |發生         |fāshēng  |phát sinh    |phát sinh/sinh ra/xảy ra/sản sinh/phôi thai/trứng phát triển                                                                                                                                               |
|63   |141         |254.63   |评论  |評論         |pínglùn  |bình luận    |bình luận/nhận xét/bài bình luận                                                                                                                                                                           |
|64   |143         |254.2    |企业  |企業         |qǐyè     |xí nghiệp    |xí nghiệp                                                                                                                                                                                                  |
|65   |144         |254.05   |方面  |方面         |fāngmiàn |phương diện  |phương diện/mặt/phía                                                                                                                                                                                       |
|66   |145         |252.86   |电影  |電影         |diànyǐng |điện ảnh     |điện ảnh/phim/chiếu bóng                                                                                                                                                                                   |
|67   |148         |248.2    |上海  |上海         |shànghǎi |thượng hải   |Thượng Hải/thành phố Thượng Hải                                                                                                                                                                            |
|68   |149         |247.87   |城市  |城市         |chéngshì |thành thị    |thành thị/đô thị/thành phố                                                                                                                                                                                 |
|69   |151         |244.47   |使用  |使用         |shǐyòng  |sử dụng      |sử dụng/dùng                                                                                                                                                                                               |
|70   |152         |244.28   |发表  |發表         |fābiǎo   |phát biểu    |phát biểu/tuyên bố/công bố/nói/đăng/đăng tải                                                                                                                                                               |
|71   |153         |243.38   |甚至  |甚至         |shénzhì  |thậm chí     |thậm chí/ngay cả/đến nỗi                                                                                                                                                                                   |
|72   |154         |241.01   |准备  |準備         |zhǔnbèi  |chuẩn bị     |chuẩn bị/dự định/định/định bụng                                                                                                                                                                            |
|73   |159         |239.06   |先生  |先生         |xiānshēng|tiên sinh    |tiên sinh/thầy/thầy giáo/ngài/chồng/thầy thuốc/ông lang/thầy ký/tài phú/thầy bói                                                                                                                           |
|74   |162         |237.82   |结果  |結果         |jiéguǒ   |kết quả      |kết quả/ra quả/ra trái/rút cuộc/thành quả/hậu quả/tác động/kết liễu/giết/xử                                                                                                                                |
|75   |164         |234.2    |管理  |管理         |guǎnlǐ   |quản lý      |quản lý/phụ trách/trông nom/bảo quản và sắp xếp/trông coi                                                                                                                                                  |
|76   |165         |234.01   |突然  |突然         |tūrán    |đột nhiên    |đột nhiên/bỗng nhiên/bất thình lình/chợt                                                                                                                                                                   |
|77   |166         |230.59   |选择  |選擇         |xuǎnzé   |tuyển trạch  |lựa chọn/tuyển trạch/tuyển chọn                                                                                                                                                                            |
|78   |167         |228.87   |回复  |回復         |húifù    |hồi phục     |hồi phục/trả lời/phúc đáp/hồi âm/phục hồi/khôi phục                                                                                                                                                        |
|79   |169         |228.33   |父亲  |父親         |fùqīn    |phụ thân     |phụ thân/bố/cha/ba                                                                                                                                                                                         |
|80   |172         |226.59   |声音  |聲音         |shēngyīn |thanh âm     |thanh âm/âm thanh/tiếng tăm/tiếng động                                                                                                                                                                     |
|81   |175         |223.22   |内容  |內容         |nèiróng  |nội dung     |nội dung                                                                                                                                                                                                   |
|82   |176         |221.99   |完全  |完全         |wánquán  |hoàn toàn    |hoàn toàn/đầy đủ/trọn vẹn                                                                                                                                                                                  |
|83   |178         |220.78   |文章  |文章         |wénzhāng |văn chương   |văn vẻ/văn chương/bài văn/bài báo/tác phẩm/ẩn ý/ngụ ý/biện pháp/cách làm                                                                                                                                   |
|84   |179         |220.23   |人员  |人員         |rényuán  |nhân viên    |nhân viên/công chức                                                                                                                                                                                        |
|85   |181         |219.81   |参加  |參加         |cānjiā   |tham gia     |tham gia/gia nhập/tham dự/dự/đề xuất/đưa ra/cho/góp                                                                                                                                                        |
|86   |185         |216.27   |历史  |歷史         |lìshǐ    |lịch sử      |lịch sử/trong lịch sử/ghi chép những sự việc đã qua/môn lịch sử/lịch sử học                                                                                                                                |
|87   |186         |215.99   |母亲  |母親         |mǔqīn    |mẫu thân     |mẫu thân/mẹ/má/me/u                                                                                                                                                                                        |
|88   |192         |213.58   |技术  |技術         |jìzhú    |kỹ thuật     |kỹ thuật/trang bị kỹ thuật/trang thiết bị                                                                                                                                                                  |
|89   |194         |209.72   |继续  |繼續         |jìxù     |kế tục       |tiếp tục/kế tục/kéo dài/kế thừa/tiếp nối                                                                                                                                                                   |
|90   |195         |209.43   |影响  |影響         |yǐngxiǎng|ảnh hưởng    |ảnh hưởng/bị ảnh hưởng/chịu ảnh hưởng/vô căn cứ/đồn đại                                                                                                                                                    |
|91   |196         |209.02   |服务  |服務         |fúwù     |phục vụ      |phục vụ/phụng sự                                                                                                                                                                                           |
|92   |199         |208.52   |决定  |決定         |juédìng  |quyết định   |quyết định/định đoạt/việc quyết định/tác dụng chủ đạo                                                                                                                                                      |
|93   |200         |208.42   |方式  |方式         |fāngshì  |phương thức  |phương thức/cách thức/cách/kiểu                                                                                                                                                                            |
|94   |204         |204.64   |表示  |表示         |biǎoshì  |biểu thị     |tỏ vẻ/biểu thị/bày tỏ/tỏ ý/ngỏ lời/có ý nghĩa/biểu hiện/chứng tỏ/dấu hiệu/tỏ                                                                                                                               |
|95   |210         |199.82   |国际  |國際         |guójì    |quốc tế      |quốc tế                                                                                                                                                                                                    |
|96   |213         |198.21   |专业  |專業         |zhuānyè  |chuyên nghiệp|chuyên nghiệp/môn/bộ môn/chuyên ngành                                                                                                                                                                      |
|97   |221         |192.09   |包括  |包括         |bāokuò   |bao quát     |chính là/bao quát/bao gồm/gồm/có/gồm có/tính đến/kể cả/chất chứa                                                                                                                                           |
|98   |223         |191.54   |离开  |離開         |líkāi    |ly khai      |rời đi/ly khai/rời khỏi/tách khỏi                                                                                                                                                                          |
|99   |225         |190.2    |介绍  |介紹         |jièshào  |giới thiệu   |giới thiệu/mở đầu/đưa vào/truyền vào/hiểu rõ/nắm được/rành/quen thuộc/nắm rành                                                                                                                             |
|100  |226         |190.05   |认识  |認識         |rènshì   |nhận thức    |nhận thức/biết/nhận biết                                                                                                                                                                                   |

### Top 100 non-cognates:

|index|chinese_rank|frequency|word|traditional|pinyin   |hanviet      |meaning                                                                                                                                                                                                    |
|-----|------------|---------|----|-----------|---------|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|1    |0           |2837.94  |我们  |我們         |wǒmen    |ngã môn      |chúng ta/chúng tôi/chúng tao/chúng tớ                                                                                                                                                                      |
|2    |1           |2157.07  |没有  |沒有         |méiyǒu   |một dựu/một hữu|không có/không/không bằng/không đủ/không tới/không đến/chưa/chưa từng/chưa hề                                                                                                                              |
|3    |2           |1921.32  |自己  |自己         |zìjǐ     |tự kỷ        |chính mình/chính/tự mình/bản thân/mình/nhà                                                                                                                                                                 |
|4    |3           |1674.9   |他们  |他們         |tāmen    |tha môn      |bọn họ/chúng nó/họ                                                                                                                                                                                         |
|5    |4           |1512.01  |什么  |什麼         |shíyāo   |thập yêu/thập ma|cái gì/gì/nào/gì đó/nhậm chỉ/mọi thứ/nấy/cái quái gì/hả/nào là                                                                                                                                             |
|6    |6           |1319.29  |这个  |這個         |zhègè    |giá cá/nghiện cá|này/cái này/việc này/vật này/quá/rất                                                                                                                                                                       |
|7    |7           |1219.85  |就是  |就是         |jìushì   |tựu thị      |chính là/hay/nhất định/cứ/đúng/dù cho/ngay cả...cũng                                                                                                                                                       |
|8    |9           |1048.04  |知道  |知道         |zhīdào   |tri đáo/tri đạo|biết/hiểu/rõ                                                                                                                                                                                               |
|9    |10          |1008.58  |因为  |因為         |yīnwèi   |nhân vi      |bởi vì/bởi rằng                                                                                                                                                                                            |
|10   |11          |971.9    |这样  |這樣         |zhèyáng  |giá dạng/nghiện dạng|như vậy/như thế/thế này                                                                                                                                                                                    |
|11   |12          |933.87   |时候  |時候         |shíhòu   |thời hậu/thì hậu|thời điểm/thời gian/lúc/khi                                                                                                                                                                                |
|12   |14          |889.84   |已经  |已經         |yǐjīng   |dĩ kinh      |đã muốn/đã/rồi                                                                                                                                                                                             |
|13   |15          |855.24   |不是  |不是         |bùshì    |phầu thị/bất thị/phi thị/phủ thị|không phải/điều không phải/không đúng/chỗ sai/lỗi/thất lễ/người có lỗi                                                                                                                                     |
|14   |17          |828.91   |还是  |還是         |huánshì  |hoàn thị/toàn thị|vẫn là/chính/hay là/vẫn còn/vẫn/còn/không ngờ/có lẽ/hãy cứ/nên/cứ/hoặc/hay                                                                                                                                 |
|15   |19          |770.52   |如果  |如果         |rúguǒ    |như quả      |nếu/nếu như/nếu mà/ví bằng                                                                                                                                                                                 |
|16   |21          |649.86   |开始  |開始         |kāishǐ   |khai thủy/khai thí|bắt đầu/khởi đầu/giai đoạn đầu/lúc đầu/bắt đầu tiến hành                                                                                                                                                   |
|17   |22          |621.84   |但是  |但是         |dànshì   |đãn thị      |nhưng là/thế nhưng/nhưng/mà/nhưng mà                                                                                                                                                                       |
|18   |25          |597.97   |第一  |第一         |dìyī     |đệ nhất      |thứ nhất/đệ nhất/đầu tiên/hạng nhất/bậc nhất/quan trọng nhất                                                                                                                                               |
|19   |26          |589.67   |一些  |一些         |yīxiē    |nhất ta/nhất tá|một ít/chút/một số/một phần/mấy/hơi/một chút/nhất ta                                                                                                                                                       |
|20   |27          |584.22   |这些  |這些         |zhèxiē   |giá ta/giá tá/nghiện ta/nghiện tá|này đó/những ... này                                                                                                                                                                                       |
|21   |28          |580.38   |还有  |還有         |huányǒu  |hoàn dựu/hoàn hữu/toàn dựu/toàn hữu|còn có                                                                                                                                                                                                     |
|22   |29          |575.77   |觉得  |覺得         |juédé    |giáo đắc/giác đắc|cảm thấy được/nghĩ/hiểu được/cảm thấy/thấy/cho rằng/thấy rằng                                                                                                                                              |
|23   |30          |572.1    |怎么  |怎麼         |zěnyāo   |chẩm yêu/chẩm ma|như thế nào/thế nào/sao/làm sao/thế/như thế/lắm                                                                                                                                                            |
|24   |32          |566.18   |不能  |不能         |bùnéng   |phầu nai/phầu nại/phầu năng/bất nai/bất nại/bất năng/phi nai/phi nại/phi năng/phủ nai/phủ nại/phủ năng|không thể/bất năng/không nổi/bất lực/không hiệu lực/không có khả năng/bất tài/không được phép/không đủ sức                                                                                                 |
|25   |33          |565.46   |孩子  |孩子         |háizǐ    |hài tử/hài tý|đứa nhỏ/hài tử/nhi đồng/trẻ em/trẻ con/con nít/em bé/con cái/con                                                                                                                                           |
|26   |34          |555.38   |起来  |起來         |qǐlái    |khởi lai/khởi lãi|đứng lên/ngồi dậy/đứng dậy/ngủ dậy/thức dậy/nổi dậy/vùng lên/dâng lên/lên                                                                                                                                  |
|27   |35          |534.48   |一样  |一樣         |yīyáng   |nhất dạng    |giống nhau/như nhau/cũng như/cũng thế                                                                                                                                                                      |
|28   |38          |505.86   |出来  |出來         |chūlái   |xuất lai/xuất lãi/xúy lai/xúy lãi/xích lai/xích lãi|đi ra/ra/ra đây/xuất hiện/nảy ra/nổi lên/hiện ra/lòi ra                                                                                                                                                    |
|29   |39          |500.3    |看到  |看到         |kàndào   |khán đáo     |nhìn đến/thấy/chứng kiến                                                                                                                                                                                   |
|30   |40          |492.7    |那么  |那麼         |nàyāo    |na yêu/na ma/nả yêu/nả ma|như vậy/như thế/như thế đấy/thế đó/thế đấy/thì                                                                                                                                                             |
|31   |41          |490.0    |也是  |也是         |yěshì    |dã thị       |cũng là                                                                                                                                                                                                    |
|32   |42          |486.99   |喜欢  |喜歡         |xǐhuān   |hí hoan/hí hoàn/hỉ hoan/hỉ hoàn|thích/yêu mến/ưa thích/vui mừng/mừng/vui vẻ                                                                                                                                                                |
|33   |43          |483.33   |美国  |美國         |měiguó   |mỹ quốc/mĩ quốc|nước Mỹ/Mỹ/Hoa Kỳ/Hiệp chủng quốc Hoa Kỳ/United States                                                                                                                                                     |
|34   |44          |481.97   |很多  |很多         |hěnduō   |ngận đa      |rất nhiều                                                                                                                                                                                                  |
|35   |45          |481.1    |那个  |那個         |nàgè     |na cá/nả cá  |cái kia/cái đó/cái ấy/việc ấy/ghê lắm/ấy                                                                                                                                                                   |
|36   |46          |475.54   |不会  |不會         |bùhùi    |phầu cối/phầu hội/bất cối/bất hội/phi cối/phi hội/phủ cối/phủ hội|sẽ không                                                                                                                                                                                                   |
|37   |49          |464.11   |你们  |你們         |nǐmen    |nhĩ môn      |các ngươi/các ông/các bà/các anh/các chị                                                                                                                                                                   |
|38   |50          |457.96   |这里  |這裡         |zhèlǐ    |giá lý/giá lí/nghiện lý/nghiện lí|nơi này/ở đây/nơi đây/tại đây                                                                                                                                                                              |
|39   |51          |444.28   |一起  |一起         |yīqǐ     |nhất khởi    |cùng nhau/đồng thời/cùng nơi/cùng một chỗ/cùng/tổng cộng/cả thảy                                                                                                                                           |
|40   |53          |439.65   |应该  |應該         |yìnggāi  |ứng cai/ưng cai|hẳn là/nên/cần phải/phải                                                                                                                                                                                   |
|41   |57          |427.06   |今天  |今天         |jīntiān  |kim thiên    |hôm nay/ngày hôm nay/hiện tại/trước mắt/kim thiên                                                                                                                                                          |
|42   |58          |425.12   |这么  |這麼         |zhèyāo   |giá yêu/giá ma/nghiện yêu/nghiện ma|như vậy/như thế/thế này                                                                                                                                                                                    |
|43   |59          |424.73   |只是  |只是         |zhǐshì   |kì thị/chỉ thị/chi thị/chích thị|chính là/chỉ là/chẳng qua là/chỉ/nhưng/nhưng mà                                                                                                                                                            |
|44   |61          |416.46   |然后  |然後         |ránhòu   |nhiên hậu/nhiên hấu|sau đó/tiếp đó                                                                                                                                                                                             |
|45   |63          |406.24   |可是  |可是         |kěshì    |khắc thị/khả thị|chính là/thế nhưng/nhưng là/nhưng/nhưng mà ̣/thực là/đúng là/thật là                                                                                                                                       |
|46   |65          |404.45   |一直  |一直         |yīzhí    |nhất trực    |vẫn/thẳng/thẳng tuốt/luôn luôn/suốt/liên tục/từ                                                                                                                                                            |
|47   |67          |400.22   |而且  |而且         |érqiě    |nhi thư/nhi thả|hơn nữa/mà còn/với lại                                                                                                                                                                                     |
|48   |68          |395.03   |需要  |需要         |xūyào    |nhu yếu/nhu yêu|cần/phải/yêu cầu/sự đòi hỏi                                                                                                                                                                                |
|49   |71          |388.91   |只有  |只有         |zhǐyǒu   |kì dựu/kì hữu/chỉ dựu/chỉ hữu/chi dựu/chi hữu/chích dựu/chích hữu|chỉ có                                                                                                                                                                                                     |
|50   |76          |373.89   |为了  |為了         |wèile    |vi liễu/vi liệu|vì/để                                                                                                                                                                                                      |
|51   |77          |370.21   |告诉  |告訴         |gàosù    |cáo tố       |nói cho/tố cáo/tố giác/đi kiện                                                                                                                                                                             |
|52   |78          |368.51   |认为  |認為         |rènwèi   |nhận vi      |cho rằng/cho là                                                                                                                                                                                            |
|53   |80          |365.25   |学校  |學校         |xuéxiào  |học hiệu/học giáo/học hào|trường học/nhà trường                                                                                                                                                                                      |
|54   |81          |364.72   |老师  |老師         |lǎoshī   |lão sư       |lão sư/thầy cô giáo/bậc thầy                                                                                                                                                                               |
|55   |83          |361.48   |不要  |不要         |bùyào    |phầu yếu/phầu yêu/bất yếu/bất yêu/phi yếu/phi yêu/phủ yếu/phủ yêu|không cần/không nên/muốn/đừng/cấm/không được/chớ/cố gắng đừng                                                                                                                                              |
|56   |84          |354.04   |以后  |以後         |yǐhòu    |dĩ hậu/dĩ hấu|về sau/sau đó/sau này/sau khi                                                                                                                                                                              |
|57   |85          |351.36   |虽然  |雖然         |sūirán   |tuy nhiên    |tuy rằng/mặc dù/tuy là                                                                                                                                                                                     |
|58   |88          |343.75   |之后  |之後         |zhīhòu   |chi hậu/chi hấu|lúc sau/lúc/khi/sau/sau khi/sau đó                                                                                                                                                                         |
|59   |90          |342.76   |比较  |比較         |bǐjiào   |bì giảo/bì giếu/bì giác/bỉ giảo/bỉ giếu/bỉ giác/bí giảo/bí giếu/bí giác/tỉ giảo/tỉ giếu/tỉ giác|có điều,so sánh/tương đối/dường như/so sánh/so với/hơn/khá                                                                                                                                                 |
|60   |92          |336.8    |为什么 |為什麼        |wèishíyāo|vi thập yêu/vi thập ma|vì cái gì/vì sao/tại sao                                                                                                                                                                                   |
|61   |99          |317.6    |有些  |有些         |yǒuxiē   |dựu ta/dựu tá/hữu ta/hữu tá|có chút/có/có một số/có một ít/hơi/có phần                                                                                                                                                                 |
|62   |100         |315.95   |到了  |到了         |dàole    |đáo liễu/đáo liệu|tới rồi                                                                                                                                                                                                    |
|63   |101         |315.57   |后来  |後來         |hòulái   |hậu lai/hậu lãi/hấu lai/hấu lãi|sau lại/về sau/sau này/sau/sau đó/đến sau/trưởng thành sau/kế thừa/kế tiếp/kế nghiệp                                                                                                                       |
|64   |102         |313.48   |那些  |那些         |nàxiē    |na ta/na tá/nả ta/nả tá|này/những...ấy/những... đó/những... kia                                                                                                                                                                    |
|65   |103         |305.6    |其他  |其他         |qítā     |ký tha/kì tha/kỳ tha/ki tha|mặt khác/cái khác/khác                                                                                                                                                                                     |
|66   |105         |300.65   |同学  |同學         |tóngxué  |đồng học     |cùng học/học chung/bạn học/đồng môn/bạn cùng lớp/học trò/học sinh                                                                                                                                          |
|67   |107         |296.99   |真的  |真的         |zhēnde   |chân đích/chân để|thật sự/thực sự                                                                                                                                                                                            |
|68   |110         |290.48   |成为  |成為         |chéngwèi |thành vi     |trở thành/biến thành/trở nên                                                                                                                                                                               |
|69   |113         |286.96   |妈妈  |媽媽         |māmā     |ma ma/ma mụ/mụ ma/mụ mụ|mụ mụ/mẹ/má/me/u/bầm/mẫu thân/mợ                                                                                                                                                                           |
|70   |114         |283.65   |许多  |許多         |xǔduō    |hứa đa/hổ đa/hử đa|rất nhiều/nhiều                                                                                                                                                                                            |
|71   |121         |277.91   |于是  |於是         |yúshì    |hu thị/ư thị/vu thị/ô thị|vì thế/Vì vậy/thế là/ngay sau đó/liền/bèn                                                                                                                                                                  |
|72   |122         |275.85   |男人  |男人         |nánrén   |nam nhân     |nam nhân/trượng phu/chồng/đàn ông                                                                                                                                                                          |
|73   |123         |274.71   |晚上  |晚上         |wǎnshàng |vãn thướng/vãn thượng|buổi tối/ban đêm/đêm tối                                                                                                                                                                                   |
|74   |127         |268.65   |回来  |回來         |húilái   |hồi lai/hồi lãi/hối lai/hối lãi|trở về/quay lại/về/trở lại/quay về trở lại/quay về                                                                                                                                                         |
|75   |134         |260.95   |下来  |下來         |xiàlái   |hạ lai/hạ lãi/há lai/há lãi|xuống dưới/xuống tới/xuống/lại/tiếp                                                                                                                                                                        |
|76   |135         |260.73   |由于  |由於         |yóuyú    |do hu/do ư/do vu/do ô|bởi vì/bởi/do                                                                                                                                                                                              |
|77   |136         |258.9    |如何  |如何         |rúhé     |như hà       |như thế nào/làm sao/thế nào/ra sao                                                                                                                                                                         |
|78   |137         |258.79   |有人  |有人         |yǒurén   |dựu nhân/hữu nhân|có người                                                                                                                                                                                                   |
|79   |139         |257.62   |一般  |一般         |yībān    |nhất ban/nhất bàn/nhất bát|bình thường/giống nhau/như nhau/một loại/một thứ/thông thường/phổ biến                                                                                                                                     |
|80   |142         |254.39   |任何  |任何         |rènhé    |nhậm hà/nhâm hà|gì/bất luận cái gì                                                                                                                                                                                         |
|81   |146         |252.14   |小时  |小時         |xiǎoshí  |tiểu thời/tiểu thì|giờ/tiếng đồng hồ/giờ đồng hồ                                                                                                                                                                              |
|82   |147         |251.0    |别人  |別人         |biérén   |biệt nhân    |người khác/kẻ khác/người ta                                                                                                                                                                                |
|83   |150         |247.07   |必须  |必須         |bìxū     |tất tu       |phải/nhất định phải/nhất thiết phải                                                                                                                                                                        |
|84   |155         |240.99   |对于  |對於         |dùiyú    |đối hu/đối ư/đối vu/đối ô|đối với/về..                                                                                                                                                                                               |
|85   |156         |240.49   |心里  |心裡         |xīnlǐ    |tâm lý/tâm lí|trong lòng/ngực/trong tư tưởng/trong đầu/trong bụng                                                                                                                                                        |
|86   |157         |240.31   |有点  |有點         |yǒudiǎn  |dựu điểm/hữu điểm|có điểm/chút/có ít/có chút/hơi/có phần                                                                                                                                                                     |
|87   |158         |239.65   |作为  |作為         |zuòwèi   |tác vi       |làm/là/hành vi/hành động/thành tích/thành tựu/có thành tích/có triển vọng/làm nên/làm được/việc nên làm/cho rằng/xem như/coi như/với tư cách/lấy tư cách                                                   |
|88   |160         |238.82   |眼睛  |眼睛         |yǎnjīng  |nhãn tình    |ánh mắt/con mắt                                                                                                                                                                                            |
|89   |161         |237.93   |人们  |人們         |rénmen   |nhân môn     |mọi người/người ta/nhân dân                                                                                                                                                                                |
|90   |163         |234.7    |那里  |那裡         |nàlǐ     |na lý/na lí/nả lý/nả lí|nơi đó/nơi nào/đó/chỗ ấy/chỗ đó/nơi ấy                                                                                                                                                                     |
|91   |168         |228.36   |看见  |看見         |kànjiàn  |khán hiện/khán kiến|thấy/trông thấy/thấy được/nhìn thấy                                                                                                                                                                        |
|92   |170         |227.89   |其中  |其中         |qízhōng  |ký trúng/ký trung/kì trúng/kì trung/kỳ trúng/kỳ trung/ki trúng/ki trung|trong đó                                                                                                                                                                                                   |
|93   |171         |227.86   |多少  |多少         |duōshǎo  |đa thiếu/đa thiểu|nhiều ít/bao nhiêu/hoặc nhiều hoặc ít/hoặc ít hoặc nhiều/hơi/hơi hơi/có phần/mấy/bao nhiêu... bấy nhiêu/bấy nhiêu                                                                                          |
|94   |173         |224.67   |得到  |得到         |dédào    |đắc đáo      |được đến/xong/đạt được/được/nhận được                                                                                                                                                                      |
|95   |174         |224.18   |或者  |或者         |huòzhě   |hoặc giả     |hoặc là/có lẽ/có thể/chắc là/hoặc                                                                                                                                                                          |
|96   |177         |221.32   |考试  |考試         |kǎoshì   |khảo thí     |cuộc thi/thi/kiểm tra/sát hạch                                                                                                                                                                             |
|97   |180         |220.14   |能够  |能夠         |nénggòu  |nai cú/nai hú/nại cú/nại hú/năng cú/năng hú|có thể/có khả năng/có điều kiện                                                                                                                                                                            |
|98   |182         |219.2    |之间  |之間         |zhījiān  |chi gian     |trong lúc đó                                                                                                                                                                                               |
|99   |183         |218.69   |以及  |以及         |yǐjí     |dĩ cập       |cùng với/và/cùng                                                                                                                                                                                           |
|100  |184         |217.75   |因此  |因此         |yīncǐ    |nhân thử     |bởi vậy/bởi vì/do đó/vì vậy                                                                                                                                                                                |
