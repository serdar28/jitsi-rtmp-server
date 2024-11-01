# Debian tabanlı bir görüntü kullanıyoruz
FROM debian:bullseye

# Gerekli paketleri kuruyoruz
RUN apt-get update && \
    apt-get install -y \
    ffmpeg \
    nginx \
    libnginx-mod-rtmp \
    xz-utils \
    libxml-xpath-perl \
    curl \
    zsh && \
    rm -rf /var/lib/apt/lists/*

# Gerekli dizinleri ve boş dosyaları oluşturuyoruz
RUN mkdir -p /usr/local/sy/recordings \
             /usr/local/sy/livestream/stat \
             /media/frames && \
    touch /usr/local/sy/livestream/index.html && \
    chown www-data:www-data /usr/local/sy -R

# stat.xsl dosyasını projedeki bir klasörden kopyalıyoruz
COPY sy/livestream/stat/rtmp_stat.xsl /usr/local/sy/livestream/stat/rtmp_stat.xsl
RUN chown www-data:www-data /usr/local/sy/livestream/stat/rtmp_stat.xsl

# Gerekli araçları kopyalıyoruz (script dizinindeki dosyalar)
COPY scripts/create-frames /usr/local/bin/create-frames
COPY scripts/mark-frames /usr/local/bin/mark-frames
COPY scripts/handle-recording /usr/local/bin/handle-recording

RUN chmod 755 /usr/local/bin/create-frames \
              /usr/local/bin/mark-frames \
              /usr/local/bin/handle-recording

# Gerekli dizinleri oluşturuyoruz
RUN mkdir -p /usr/local/sy/recordings \
    /usr/local/sy/livestream/stat \
    /media/frames && \
    chown www-data: /usr/local/sy/recordings && \
    chown www-data: /usr/local/sy/livestream -R

# Gerekli araçları kopyalıyoruz (script dizinindeki dosyalar)
COPY script/create-frames /usr/local/bin/create-frames
COPY script/mark-frames /usr/local/bin/mark-frames
COPY script/handle-recording /usr/local/bin/handle-recording

RUN chmod 755 /usr/local/bin/create-frames \
    /usr/local/bin/mark-frames \
    /usr/local/bin/handle-recording

# Özel Nginx yapılandırma dosyaları ekliyoruz
COPY etc/nginx/access_list_http.conf /etc/nginx/
COPY etc/nginx/access_list_rtmp_play.conf /etc/nginx/
COPY etc/nginx/access_list_rtmp_publish.conf /etc/nginx/
COPY etc/nginx/modules-available/90-rtmp.conf /etc/nginx/modules-available/
RUN ln -s /etc/nginx/modules-available/90-rtmp.conf /etc/nginx/modules-enabled/

# Nginx sites-available dizinine özel bir yapılandırma dosyası ekleyelim
COPY etc/nginx/sites-available/livestream.conf /etc/nginx/sites-available/
RUN ln -s /etc/nginx/sites-available/livestream.conf /etc/nginx/sites-enabled/ && \
    rm /etc/nginx/sites-enabled/default && \
    sed -i 's/^worker_processes .*$/worker_processes 1;/' /etc/nginx/nginx.conf

# Bağlantı dosyaları
RUN ln -s /media/frames /usr/local/sy/livestream/frames

# Çalıştırılacak komut
CMD ["nginx", "-g", "daemon off;"]