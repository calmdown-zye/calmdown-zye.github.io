# CLAUDE.md

Jekyll + Chirpy 테마(`jekyll-theme-chirpy ~> 7.3`)로 만든 개인 블로그 (CSI | Calm Statistical Inference).
배포: `main`에 푸시하면 `.github/workflows/pages-deploy.yml`이 빌드, htmlproofer 검사, GitHub Pages 배포를 한다.

## 구조
- `_config.yml` 사이트 설정. 155행 아래(`kramdown`, `collections`, `defaults` 등)는 건드리지 않는다.
- `index.html` 새 첫 화면(`/`). Chirpy 레이아웃 없는 독립 페이지(`layout: null`)이고 CSS/JS가 안에 들어 있다. Liquid로 처리되므로 인라인 CSS/JS에 `{{`, `{%`를 쓰지 않는다.
- `blog/index.html` 기존 Chirpy 홈(글 목록)이 옮겨온 곳(`/blog/`). 페이지 넘김은 `_config.yml`의 `paginate_path: "/blog/page:num/"`. `jekyll-paginate`는 경로가 가장 긴 `index.html`을 템플릿으로 고르므로 `blog/index.html`이 선택된다.
- `_includes/sidebar.html`, `topbar.html`, `post-paginator.html` gem에서 복사해 고친 오버라이드(블로그 홈 링크 `/` → `/blog/`). 각 파일 맨 위 주석에 바꾼 곳이 적혀 있다. Chirpy 버전을 올릴 때 gem 원본과 diff를 확인한다.
- `_data/research.yml` 첫 화면 RESEARCH 섹션 데이터. `[]`면 섹션과 메뉴가 숨겨진다.
- `_posts/` 글. 파일명 `YYYY-MM-DD-제목.md`.
- `_tabs/` 사이드바 메뉴 페이지(archives, categories, tags). front matter의 `order`가 순서.
- `_data/contact.yml`, `_data/share.yml` 사이드바 연락처 아이콘, 글 하단 공유 버튼.
- `_plugins/posts-lastmod-hook.rb` git 이력으로 `last_modified_at` 계산 (그래서 CI는 `fetch-depth: 0`).
- `assets/lib` 서브모듈(`chirpy-static-assets`). 지금은 비어 있고 CDN을 쓴다.
- `design/` 리디자인 시안 원본. 로컬 전용이라 `.gitignore`에 들어 있다. 시안 도구 형식이라 그대로 쓰지 않고 색, 글꼴, 배치, 알고리즘만 참고한다.

## 로컬 실행
- Ruby 3.3.12 (rbenv, `.ruby-version`). 시스템 Ruby 2.6은 쓰지 않는다.
- 셸에서 `ruby -v`가 3.3.12가 아니면 `eval "$(rbenv init - zsh)"`가 `~/.zshrc`에 없는 것이다. 없을 때는 명령 앞에 `export PATH="$HOME/.rbenv/shims:$PATH"`를 붙인다.
- 의존성은 `vendor/bundle`에 설치된다(`bundle config set --local path vendor/bundle`, git에서 제외됨).
- `bundle install` 후 `bash tools/run.sh` → http://127.0.0.1:4000
- 검사: `JEKYLL_ENV=production bundle exec jekyll b && bundle exec htmlproofer _site --disable-external` (CI와 같은 방식)

## 지켜야 할 것
- 글 주소 `/posts/:title/` (`_config.yml`의 `defaults`의 `permalink`)는 바꾸지 않는다.
- 리디자인은 `redesign` 브랜치에서 한다. `main`에 푸시하면 바로 배포되므로 PR로만 합친다.
- 강조색은 CSS 변수 `--accent` 한 곳에서만 정의한다.

## 작업 방식 (전역 지침 재확인)
- 코드 주석에는 추상적 설명 대신 구체적인 입력/출력 예시를 넣는다. 예: `// ridge(0, 640) → 약 389.6`
- 큰 변경 전에는 무엇을 바꿀지 먼저 3줄로 설명한다.
- 커밋 메시지 끝에 Claude Code가 지정한 Co-Authored-By 줄을 넣는다.
