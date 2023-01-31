---
title: Tistory에서 GitHub Pages로 옮기는 여정
toc: true
categories:
  - 기타
tags:
  - Tistory
  - GitHub Pages
  - Migration
---

이제 GitHub Pages 설치를 완료했으니 기존 Tistory에 작성한 글들을 옮기겠습니다.
아래 사용된 코드([Migration Code](https://github.com/hyojupark/tistory-to-github-page-migration))은 **GitHub**에 공개해놨습니다. (도움이 됐다면 Star를 눌러주세요😁)

## Tistory 게시글 정보 추출
여러 방향으로 고민하다 python **BeautifulSoup**으로 수집하는 방법을 선택했습니다. BeautifulSoup으로 수집해야하는 항목은 아래와 같습니다.

1. 제목
2. 본문
3. 이미지 (본문에 있는)
4. 작성 날짜 (년, 월, 일)
5. 카테고리
6. 태그

제목과 본문, 작성 날짜, 카테고리, 태그는 클래스가 정의되어있어 쉽게 수집했습니다. 이미지는 본문 클래스 내에 있는 `<img>` 태그를 전부 가져와서 저장했습니다.
```python
import requests
from bs4 import BeautifulSoup as bs

blog_post_url = 'https://hyostech.tistory.com/5'
html = requests.get(blog_post_url)

title = soup.select_one(".article-header .title-article")
print(title.text)

content = soup.select_one(".article-view .contents_style")
print(content.text)

date = soup.select_one(".article-header .date")
year, month, day, _ = date.text.split('.')
print(year, month, day)

tags = soup.select(".article-tag .box-tag a")
print(', '.join(list(map(lambda tag: tag.text, tags))))

images = content.select("img")
print('\n'.join(list(map(lambda image: image['src'], images))))
```

## 게시글 포맷 변경
### 본문 markdown 변환

Tistory에도 markdown 변환 기능이 있지만 수집 과정에서 가져올 수 없어 `markdownify` 패키지를 설치해서 변환했습니다. 
```python
...
content = soup.select_one(".article-view .contents_style")
content_md = markdownify.markdownify(str(content), heading_style="ATX")
print(content_md)
```

markdown 파일은 제목에 번역기를 돌릴까 하다가 포스트 번호(no)로 indexing했습니다. 최종 포맷은 `{year}-{month}-{day}-tistory-post-{no}.md`입니다.

### 이미지 저장

이미지는 markdown 파일명을 가지고 폴더로 나눠서 저장했습니다. 각 이미지에 이름은 `img-{n}.png` 형식으로 저장했습니다.
이대로 끝내면 본문의 이미지 경로가 tistory로 사용됩니다. GitHub Pages 내에 저장될 이미지 경로로 치환하는 작업을 추가로 진행합니다.
```python
...
md_base_name = f'{year.strip()}-{month.strip()}-{day.strip()}-tistory-post-{page_no}'
image_save_dir = 'assets/images/posts'  # 이미지가 저장될 경로
image_dir = os.path.join(image_save_dir, md_base_name)

for n, image in enumerate(content.select("img"), start=1):
    src = image['src']
    image_path = os.path.join(image_dir, f'img-{n}.png')
    open(image_path, 'wb').write(requests.get(src).content)
    content_md = content_md.replace(src, f'/{image_path}')
```

### markdown metadata 추가

markdown 최상단에 해당 포스트의 제목, 카테고리, 태그 정보를 추가합니다. 제목에 `:` 기호를 사용할 수 없기 때문에 코드로 치환하고, 태그는 포맷에 맞게 변환에서 저장합니다.
마지막으로 markdown에 meatadata를 붙여서 파일로 저장합니다.
```python
...
tags = '\n  '.join(list(map(lambda tag: f'- {tag.text}', tags)))

metadata = f"""---
title: {title.text.replace(':', '&#58;')}
toc: true
categories:
  - {category.text}
tags:
  {tags}
---
"""

content_md = metadata + content_md
open(os.path.join('_posts', f'{md_base_name}.md'), 'w').write(content_md)
```

## Tistory 포스트 수집

게시글은 `시작 포스트 번호` ~ `마지막 포스트 번호`를 지정해서 수집했습니다. 중간 중간에 빈 포스트(삭제한 포스트) 번호가 있기 때문에 `404` 예외처리를 같이 추가했습니다. 수집 과정은 `tqdm`을 쓰는 것 보다 직접 print를 이용하면 직관적으로 출력할 수 있어 print로 남겼습니다.

[최종 코드 링크](https://github.com/hyojupark/tistory-to-github-page-migration/blob/main/main.ipynb)
```python
blog_url = 'https://hyostech.tistory.com'
image_save_dir = 'assets/images/posts'
start_page_no = 1
end_page_no = 100

os.makedirs('_posts', exist_ok=True)
for page_no in range(start_page_no, end_page_no + 1):
    print(page_no, ' ... ', end='')
    html = requests.get(f'{blog_url}/{page_no}')

    if html.status_code == 404:
        print('404')
        continue

    soup = bs(html.text, "html.parser")

    title = soup.select_one(".article-header .title-article")
    print(title.text, ' ... ', end='')
    ...
```

이제 코드를 실행하면 Tistory에 있는 포스트가 `markdown`, `image` 파일로 각각 저장됩니다. 코드에서는 `_posts` 경로와 `assets` 내 이미지 경로를 미리 맞춰놨기 때문에 폴더째로 복사해서 붙여넣으면 바로 포스트가 옮겨집니다. 이제 직접 옮겨진 포스트를 보고 확인하면 됩니다.

![blog posts](/assets/images/posts/2023-1-31-tistory-to-github-pages-migration/blog_posts.png)


## 안되는 것들
1. **이미지, 글 정렬**
  - 보통 이미지를 가운데 정렬하고 아래 캡션을 직접 썻는데, 이 스타일이 다 풀렸습니다.
  - 이미지에는 `{: .align-center}`, 아래 캡션에는 `{: .text-center}`를 직접 추가했습니다.
2. **Code Block Style**
  - 각 Code Block이 어떤 언어로 돼있는지 몰라 plain text로 적용돼있습니다. (Tistory 포스트에는 다 적용했는데, markdownify가 이 부분은 가져와주지 못해 아쉬웠습니다.)
3. **다수의 개행 삭제**
  - markdown으로 변환해보니 개행이 불필요하게 많이 들어갔는데, 포스트들을 직접 다 확인해볼 겸 지우지 않았습니다. (마지막에 조금 귀찮아진 것도 있습니다.. 😂)


저는 나머지 포스트들은 시간을 두고 천천히 고쳐보려합니다. 하나씩 고치다보니까 전에 글들을 얼마나 못썻는지, 스타일은 어떻게 바꾸는게 좋을지가 보여서 좋은 것 같습니다😁