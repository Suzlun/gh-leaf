FROM buildpack-deps:bookworm-scm

# user 定義
ARG USERNAME=gh-leaf
ARG USER_UID=3000
ARG USER_GID=3000

# user 作成
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

WORKDIR /workspace

ARG TEXLIVE_MIRROR=https://tug.ctan.org/systems/texlive/tlnet

ENV PATH=/usr/local/texlive/bin:$PATH

WORKDIR /texlive-install

COPY texlive.profile .

# texliveをインストール
RUN wget ${TEXLIVE_MIRROR}/install-tl-unx.tar.gz \
  && tar -xf install-tl-unx.tar.gz --strip-components 1 \
  && ./install-tl -profile texlive.profile --location ${TEXLIVE_MIRROR} \
  && ln -sf /usr/local/texlive/*/bin/* /usr/local/texlive/bin

# latexmkのインストール
RUN tlmgr install latexmk

# GhostscriptとGnuplotをインストール
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
  && apt-get -y install --no-install-recommends ghostscript gnuplot \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /usr/local/texlive/texmf-local/tex/latex/gnuplot

# gnuplot-lua-tikzをインストール
RUN gnuplot -e 'set term tikz createstyle' \
  && mktexlsr

WORKDIR /

RUN rm -rf /texlive-install

# すべてのインストール作業完了後にユーザーを切り替え
USER $USERNAME

CMD ["bash"]