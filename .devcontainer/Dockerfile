FROM rocker/tidyverse

# need this to install tlverse 
ARG GITHUB_PAT

# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="false"

# Location and expected SHA for common setup script - SHA generated on release
ARG COMMON_SCRIPT_SOURCE="https://raw.githubusercontent.com/microsoft/vscode-dev-containers/master/script-library/common-debian.sh"
ARG COMMON_SCRIPT_SHA="dev-mode"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=docker
ARG USER_UID=1001
ARG USER_GID=$USER_UID

# Configure apt and install packages
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends apt-utils ca-certificates dialog wget \
    # Verify git, common tools / libs installed, add/modify non-root user, optionally install zsh
    && wget -q -O /tmp/common-setup.sh $COMMON_SCRIPT_SOURCE \
    && if [ "$COMMON_SCRIPT_SHA" != "dev-mode" ]; then echo "$COMMON_SCRIPT_SHA /tmp/library-scripts/common-setup.sh" | sha256sum -c - ; fi \
    && /bin/bash /tmp/common-setup.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" \
    && apt-get -y install libzip-dev libcurl4-openssl-dev libssl-dev libxml2-dev libz-dev \
    r-cran-runit \
    libglpk-dev

# install radian, r-languageserver, and rstudioapi addins
RUN apt-get -y install python3-pip \
    && pip3 install -U radian \
    && Rscript -e 'install.packages(c("languageserver", "rstudioapi"))'

# install R packages that I often use
RUN apt-get -y install python3-pip \
    && pip3 install -U radian \
    && Rscript -e 'install.packages(c("data.table", "targets", "drake", "visNetwork"))'

# clean up unnecessary files
RUN apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/*

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update \
#     && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>

# Add tlverse R libraries (requires a GITHUB_PAT to be set in the host env for now)
ARG GITHUB_PAT
RUN export GITHUB_PAT=$GITHUB_PAT
RUN R -e "devtools::install_github('tlverse/tlverse')"
RUN export GITHUB_PAT=""