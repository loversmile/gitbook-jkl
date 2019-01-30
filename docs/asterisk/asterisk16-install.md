asterisk-16.1.1

./configure

configure: error: *** Please install the 'libedit' development package.

sudo apt-get install libedit-dev



module load res_crypto
module load res_http_websocket
module load res_pjsip_transport_websocket
codec_opus (optional but highly recommended for high quality audio)

module load res_srtp.so


./configure --prefix=/opt/asterisk-16 --with-jansson-bundled
