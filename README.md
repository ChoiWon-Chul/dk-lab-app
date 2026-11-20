# dk-lab-app — Jenkins 입력용 소스 저장소

Jenkins가 이 저장소의 소스를 가져와 Docker 이미지를 빌드하고 GitOps 저장소를 갱신합니다.

- `app/index.html` / `app/Dockerfile`: Nginx 정적 웹페이지
- `Jenkinsfile`: Checkout → Build → Smoke test → Registry Push → GitOps manifest commit
- GitOps 저장소 주소: `https://github.com/ChoiWon-Chul/dk-lab-gitops.git`
- Jenkins GitHub 인증정보는 Jenkins Credentials에 `github-gitops` ID로 등록합니다.
- **절대 추가하지 말 것**: GitHub PAT, 비밀번호, 실서버 설정, 회사 내부자료, SSH 개인키
