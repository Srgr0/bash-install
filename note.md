#項目名、情報タイトル、その他の情報
#ユーザーの操作を必要としない情報系
#緑の2
tput setaf 2; echo -n "arch;";

#エラー
#赤の1
tput setaf 1; echo "    NG. $(uname -m) is unsupported architecture.";

#セクション最初
#黄色の3
tput setaf 3; echo "Install Method"; tput setaf 7;
