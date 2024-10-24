<!-- wp:heading {"level":1} -->
<h1 class="wp-block-heading"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#server-preparation"></a>Server preparation</h1>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>apt update &amp;&amp; apt upgrade -y</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="install-go"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#install-go"></a>Install GO v1.20.3</h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph -->

<!-- wp:code -->
<pre class="wp-block-code"><code>ver="1.20.3"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" &gt;&gt; $HOME/.bash_profile
source $HOME/.bash_profile
go version</code></pre>
<!-- /wp:code -->

<!-- wp:heading -->
<h2 class="wp-block-heading" id="node-installation"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#node-installation"></a>Node Setup</h2>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>git clone https://github.com/warden-protocol/wardenprotocol &amp;&amp; cd wardenprotocol

wget https://github.com/warden-protocol/wardenprotocol/releases/download/v0.5.2/wardend_Linux_x86_64.zip
unzip wardend_Linux_x86_64.zip
rm -rf wardend_Linux_x86_64.zip
chmod +x wardend
mv $HOME/wardenprotocol/wardend $HOME/go/bin

wardend version --long | grep -e version -e commit
# version: 0.5.2
# commit: e9ba0b8a2aa05787360270df19480c33429843d4</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="we-initialize-the-node-to-create-the-necessary-configuration-files"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#we-initialize-the-node-to-create-the-necessary-configuration-files"></a>We initialize the node to create the necessary configuration files</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>wardend init NodesRun --chain-id chiado_10010-1</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="download-genesis"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#download-genesis"></a>Download Genesis</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>wget -O $HOME/.warden/config/genesis.json https://server-4.itrocket.net/testnet/warden/genesis.json</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="at-this-stage-we-can-download-the-address-book"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#at-this-stage-we-can-download-the-address-book"></a>Download the address book</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>wget -O $HOME/.warden/config/addrbook.json  https://server-4.itrocket.net/testnet/warden/addrbook.json</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="set-up-node-configuration"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#set-up-node-configuration"></a>Set<strong> </strong>up node configuration</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>wardend config set client chain-id chiado_10010-1
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"25000000award\"/;" ~/.warden/config/app.toml
external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.warden/config/config.toml
peers="b14f35c07c1b2e58c4a1c1727c89a5933739eeea@warden-testnet-peer.itrocket.net:18656,2d2c7af1c2d28408f437aef3d034087f40b85401@52.51.132.79:26656,fcaffd41eb7e3647fa953607449ff5e371c236b8@195.26.245.67:31656,5461e7642520a1f8427ffaa57f9d39cf345fcd47@54.72.190.0:26656,e1ea15d3c460eb9ace279b0b7665015d3c5d2b9e@135.181.210.171:21406"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.warden/config/config.toml
seeds="8288657cb2ba075f600911685670517d18f54f3b@warden-testnet-seed.itrocket.net:18656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.warden/config/config.toml</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="optional-set-up-pruning"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#optional-set-up-pruning"></a>(OPTIONAL) Set up pruning</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.warden/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.warden/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.warden/config/app.toml</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="optional-set-up-indexer"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#optional-set-up-indexer"></a>(OPTIONAL) Set up indexer</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.warden/config/config.toml</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="optional-enable-disable-snapshots"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#optional-enable-disable-snapshots"></a>(OPTIONAL) Enable/Disable Snapshots</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.warden/config/app.toml</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4 class="wp-block-heading" id="create-a-service-file"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#create-a-service-file"></a>Create a service file</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>tee /etc/systemd/system/wardend.service &gt; /dev/null &lt;&lt;EOF
&#91;Unit]
Description=wardend
After=network-online.target

&#91;Service]
User=$USER
ExecStart=$(which wardend) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

&#91;Install]
WantedBy=multi-user.target
EOF</code></pre>
<!-- /wp:code -->

<!-- wp:code -->
<pre class="wp-block-code"><code>systemctl daemon-reload
systemctl enable wardend
systemctl restart wardend &amp;&amp; journalctl -u wardend -f -o cat</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p><strong>If peers do not cling for a long time or you see </strong><strong>errors error: wrong Block.Header.AppHash</strong><strong>, you need to use State sync or boot from a Snapshot</strong></p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3 class="wp-block-heading" id="creating-a-validator"><a href="https://utsa.gitbook.io/services/testnet/warden-protocol/installation#creating-a-validator"></a><strong>Creating a validator</strong></h3>
<!-- /wp:heading -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Get your pubkey</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>wardend tendermint show-validator</code></pre>
<!-- /wp:code -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Create validator.json</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>nano $HOME/.warden/validator.json</code></pre>
<!-- /wp:code -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Insert our config</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>{
  "pubkey": {#pubkey},
  "amount": "1000000000000000000award",
  "moniker": "&lt;MONIKER&gt;",
  "identity": "",
  "website": "",
  "security": "",
  "details": "",
  "commission-rate": "0.05",
  "commission-max-rate": "0.5",
  "commission-max-change-rate": "0.5",
  "min-self-delegation": "1"
}</code></pre>
<!-- /wp:code -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list"><!-- wp:list-item -->
<li>Send the transaction</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>wardend tx staking create-validator $HOME/.warden/validator.json \
    --from=&lt;key-name&gt; \
    --chain-id=chiado_10010-1 \
    --fees 250000000000000award -y  --gas auto --gas-adjustment 1.6</code></pre>
<!-- /wp:code -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph -->
