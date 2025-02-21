# Nodejs Demos

## 环境

```bash
# node -v
v20.9.0
```



# 1、Console



```ts
// 打印，可传参数
console.log('console.log');
console.info('console.info');
console.debug('console.debug: %d', 5);
console.error('console.error');
console.warn('console.warn');

// 断言
console.assert(false, 'hello');

// 计数
console.count('a');
console.count('a');
console.count('b');
// 重置计数
console.countReset('a');
console.count('a');


// 打印table
console.table([
    {
        a: 1,
        b: 2
    },
    {
        a: 3,
        b: 4
    }
]);

// 
console.dir({
    a: {
        b: {
            c: 1,
            d: 2
        }
    },
    e: 3
});

// 缩进
console.group('hello');
console.groupCollapsed('hello');
console.groupCollapsed('hello');
console.groupEnd();

// 打印耗时
console.time('hello');
console.timeEnd('hello');

console.time('timeLog');
console.timeLog('timeLog');
console.timeLog('timeLog');
console.timeEnd('timeLog');
// 在Inspector 使用
console.timeStamp('hello');

// 在Inspector 使用
console.profile('hello');
console.profileEnd('hello');


// 清空控制台
// console.clear();

console.trace('hello');

import { Console } from 'console';
import * as FS from 'fs';
const stdout = FS.createWriteStream('./stdout.log');
const stderr = FS.createWriteStream('./stderr.log');


// const logger = new Console(stdout, stderr, true);
const logger = new Console(process.stdout, process.stderr, true);
// stdout
logger.log('log');
logger.info('info');
logger.debug('debug');
// stderr
logger.warn('warn');
logger.error('error');

/////////////output////////////////
console.log
console.info
console.debug: 5
console.error
console.warn
Assertion failed: hello
a: 1
a: 2
b: 1
a: 1
┌─────────┬───┬───┐
│ (index) │ a │ b │
├─────────┼───┼───┤
│    0    │ 1 │ 2 │
│    1    │ 3 │ 4 │
└─────────┴───┴───┘
{ a: { b: { c: 1, d: 2 } }, e: 3 }
hello
  hello
    hello
    hello: 0.033ms
    timeLog: 0.015ms
    timeLog: 0.045ms
    timeLog: 0.065ms
    Trace: hello
        at Object.<anonymous> (/home/hello/code/node/node-demos/app/console/console.js:81:9)
        at Module._compile (node:internal/modules/cjs/loader:1241:14)
        at Module._extensions..js (node:internal/modules/cjs/loader:1295:10)
        at Module.load (node:internal/modules/cjs/loader:1091:32)
        at Module._load (node:internal/modules/cjs/loader:938:12)
        at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:83:12)
        at node:internal/main/run_main_module:23:47
```



# 2、Errors

```ts
const err = new Error('error', { cause: 'cause'});
console.log('name: ', err.name);
console.log('cause: ', err.cause);
console.log('stack: ', err.stack);
console.log('message: ', err.message);
console.log(err);

//////output////////
name:  Error
cause:  cause
stack:  Error: error
    at Object.<anonymous> (/home/hello/code/node/node-demos/app/errors/errors.js:2:13)
    at Module._compile (node:internal/modules/cjs/loader:1241:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1295:10)
    at Module.load (node:internal/modules/cjs/loader:1091:32)
    at Module._load (node:internal/modules/cjs/loader:938:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:83:12)
    at node:internal/main/run_main_module:23:47
message:  error
Error: error
    at Object.<anonymous> (/home/hello/code/node/node-demos/app/errors/errors.js:2:13)
    at Module._compile (node:internal/modules/cjs/loader:1241:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1295:10)
    at Module.load (node:internal/modules/cjs/loader:1091:32)
    at Module._load (node:internal/modules/cjs/loader:938:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:83:12)
    at node:internal/main/run_main_module:23:47 {
  [cause]: 'cause'
}
```



# 3、DNS

```ts
import { log } from 'console';
import * as DNS from 'dns/promises';
const hostname = 'baidu.com';

async function test(): Promise<void> {
    const resolve = await DNS.resolve(hostname);
    console.log('resolve: ', resolve);
    const resolve4 = await DNS.resolve4(hostname);
    console.log('resolve4: ', resolve4);
    // const resolve6 = await DNS.resolve6(hostname);
    // console.log('resolve6: ', resolve6);
    const resolveAny = await DNS.resolveAny(hostname);
    console.log('resolveAny: ', resolveAny);
    // const resolveCaa = await DNS.resolveCaa(hostname);
    // console.log('resolveCaa: ', resolveCaa);
    
    // const resolveCname = await DNS.resolveCname(hostname);
    // console.log('resolveCname: ', resolveCname);
    
    // const resolveMx = await DNS.resolveMx(hostname);
    // console.log('resolveMx: ', resolveMx);
    
    // const resolveNaptr = await DNS.resolveNaptr(hostname);
    // console.log('resolveNaptr: ', resolveNaptr);
    
    // const resolveNs = await DNS.resolveNs(hostname);
    // console.log('resolveNs: ', resolveNs);
    
    // const resolvePtr = await DNS.resolvePtr(hostname);
    // console.log('resolvePtr: ', resolvePtr);
    
    // const resolveSoa = await DNS.resolveSoa(hostname);
    // console.log('resolveSoa: ', resolveSoa);
    
    // const resolveSrv = await DNS.resolveSrv(hostname);
    // console.log('resolveSrv: ', resolveSrv);
    
    const resolveTxt = await DNS.resolveTxt(hostname);
    console.log('resolveTxt: ', resolveTxt);
    
    const getServers = await DNS.getServers();
    console.log('getServers: ', getServers);
    
    
    
    
}

(async () => {
    await test();
})();

```



# 4、Events



```ts
import * as $E from 'events';

console.log($E.defaultMaxListeners);

const events = new $E.EventEmitter();
console.log('getMaxListeners: ', events.getMaxListeners());
events.setMaxListeners(20);
console.log('getMaxListeners: ', events.getMaxListeners());


console.log('listenerCount: ', events.listenerCount('hello'));
events.on('hello', () => {});
console.log('listenerCount: ', events.listenerCount('hello'));

events.on('e1', () => {
    console.log('e1');
});
events.emit('e1');
events.emit('e1');
events.once('e2', () => {
    console.log('e2');
});
events.emit('e2');
events.emit('e2');

// events.removeListener()



```



# 5、OS



```ts
import * as OS from 'os';

console.log('EOL: ', OS.EOL);
console.log('arch: ', OS.arch());
console.log('availableParallelism: ', OS.availableParallelism());
console.log('cpus: ', OS.cpus());
console.log('endianness: ', OS.endianness());
console.log('freemem: ', OS.freemem());
console.log('getPriority: ', OS.getPriority());
console.log('homedir: ', OS.homedir());
console.log('hostname: ', OS.hostname());
console.log('loadavg: ', OS.loadavg());
console.log('machine: ', OS.machine());
console.log('networkInterfaces: ', OS.networkInterfaces());
console.log('platform: ', OS.platform());
console.log('release: ', OS.release());
console.log('tmpdir: ', OS.tmpdir());
console.log('totalmem: ', OS.totalmem());
console.log('type: ', OS.type());
console.log('uptime: ', OS.uptime());
console.log('userInfo: ', OS.userInfo());
console.log('version: ', OS.version());
console.log('devNull: ', OS.devNull);

//
EOL:  

arch:  x64
availableParallelism:  8
cpus:  [
  {
    model: '13th Gen Intel(R) Core(TM) i5-13400F',
    speed: 0,
    times: { user: 243260, nice: 12320, sys: 85860, idle: 218433800, irq: 0 }
  },
  {
    model: '13th Gen Intel(R) Core(TM) i5-13400F',
    speed: 0,
    times: { user: 295020, nice: 14930, sys: 75800, idle: 218449100, irq: 0 }
  },
  {
    model: '13th Gen Intel(R) Core(TM) i5-13400F',
    speed: 0,
    times: { user: 292480, nice: 15920, sys: 118080, idle: 217747050, irq: 0 }
  },
  {
    model: '13th Gen Intel(R) Core(TM) i5-13400F',
    speed: 0,
    times: { user: 168880, nice: 12710, sys: 59700, idle: 218645860, irq: 0 }
  },
  {
    model: '13th Gen Intel(R) Core(TM) i5-13400F',
    speed: 0,
    times: { user: 173720, nice: 19150, sys: 61070, idle: 218652400, irq: 0 }
  },
  {
    model: '13th Gen Intel(R) Core(TM) i5-13400F',
    speed: 0,
    times: { user: 282930, nice: 17060, sys: 82290, idle: 218237310, irq: 0 }
  },
  {
    model: '13th Gen Intel(R) Core(TM) i5-13400F',
    speed: 0,
    times: { user: 165170, nice: 18970, sys: 56430, idle: 218473970, irq: 0 }
  },
  {
    model: '13th Gen Intel(R) Core(TM) i5-13400F',
    speed: 0,
    times: { user: 236130, nice: 13530, sys: 85840, idle: 218279360, irq: 0 }
  }
]
endianness:  LE
freemem:  7390806016
getPriority:  0
homedir:  /home/hello
hostname:  hello
loadavg:  [ 0.34, 0.2, 0.14 ]
machine:  x86_64
networkInterfaces:  {
  lo: [
    {
      address: '127.0.0.1',
      netmask: '255.0.0.0',
      family: 'IPv4',
      mac: '00:00:00:00:00:00',
      internal: true,
      cidr: '127.0.0.1/8'
    },
    {
      address: '::1',
      netmask: 'ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff',
      family: 'IPv6',
      mac: '00:00:00:00:00:00',
      internal: true,
      cidr: '::1/128',
      scopeid: 0
    }
  ],
  ens33: [
    {
      address: '192.168.43.51',
      netmask: '255.255.255.0',
      family: 'IPv4',
      mac: '00:0c:29:21:00:fa',
      internal: false,
      cidr: '192.168.43.51/24'
    },
    {
      address: 'fe80::20c:29ff:fe21:58fa',
      netmask: 'ffff:ffff:ffff:ffff::',
      family: 'IPv6',
      mac: '00:0c:29:21:00:fa',
      internal: false,
      cidr: 'fe80::20c:29ff:fe21:58fa/64',
      scopeid: 2
    }
  ]
}
platform:  linux
release:  5.4.0-166-generic
tmpdir:  /tmp
totalmem:  8814678016
type:  Linux
uptime:  219255.63
userInfo:  {
  uid: 1000,
  gid: 1000,
  username: 'hello',
  homedir: '/home/hello',
  shell: '/usr/bin/zsh'
}
version:  #183-Ubuntu SMP Mon Oct 2 11:28:33 UTC 2023
devNull:  /dev/null


```



# 6 、Path



```ts
import * as Path from 'path';

console.log(Path.basename('C:\\hello\\haha.txt'));
console.log(Path.posix.basename('C:\\hello\\haha.txt'));
console.log(Path.win32.basename('C:\\hello\\haha.txt'));

console.log('sep: ', Path.sep);
console.log('isAbsolute: ', Path.isAbsolute('/cdcd/'));
console.log('isAbsolute: ', Path.isAbsolute('./cdcd/'));

console.log('dirname: ', Path.dirname('/jiji/haha'));
console.log('extname: ', Path.extname('/haha/jiji.txt'));
console.log('join: ', Path.join('ddc', 'sdsdfsd', 'wdwsd'));
console.log('relative: ', Path.relative('/home/jiji/haha', '/home/jiji'));
console.log('normalize: ', Path.normalize('/homne/////jiji//'));
console.log('resolve: ', Path.resolve('csdcsd', 'dsss'));

console.log('toNamespacedPath: ', Path.toNamespacedPath('/home/jiji'));

// 
C:\hello\haha.txt
C:\hello\haha.txt
haha.txt
sep:  /
isAbsolute:  true
isAbsolute:  false
dirname:  /jiji
extname:  .txt
join:  ddc/sdsdfsd/wdwsd
relative:  ..
normalize:  /homne/jiji/
resolve:  /home/hello/code/node/node-demos/csdcsd/dsss
toNamespacedPath:  /home/jiji





```





# 7、URL

```ts
import * as URL from 'url';
const url = new URL.URL('https://hello:passwd@hello.com:80/jiji/haha?xxx=111&xxx=222#yyy');
console.log('hash: ', url.hash);
console.log('host: ', url.host);
console.log('hostname: ', url.hostname);
console.log('href: ', url.href);
console.log('origin: ', url.origin);
console.log('password: ', url.password);
console.log('pathname: ', url.pathname);
console.log('port: ', url.port);
console.log('protocol: ', url.protocol);
console.log('search: ', url.search);
console.log('searchParams: ', url.searchParams);
console.log('toJSON: ', url.toJSON());
console.log('toString: ', url.toString());
console.log('username: ', url.username);
console.log('domainToASCII: ', URL.domainToASCII('中文.com'));
console.log('domainToUnicode: ', URL.domainToUnicode('中文.com'));
console.log('fileURLToPath: ', URL.fileURLToPath('file:/home/h.txt'));
console.log('resolve: ', URL.resolve('dfdf/dfff/', 'ddcdcd'));
console.log('urlToHttpOptions: ', URL.urlToHttpOptions(url));
console.log('format: ', URL.format(url, {
    auth: false
}));
console.log('pathToFileURL: ', URL.pathToFileURL('/home/r.txt'));

// 
hash:  #yyy
host:  hello.com:80
hostname:  hello.com
href:  https://hello:passwd@hello.com:80/jiji/haha?xxx=111&xxx=222#yyy
origin:  https://hello.com:80
password:  passwd
pathname:  /jiji/haha
port:  80
protocol:  https:
search:  ?xxx=111&xxx=222
searchParams:  URLSearchParams { 'xxx' => '111', 'xxx' => '222' }
toJSON:  https://hello:passwd@hello.com:80/jiji/haha?xxx=111&xxx=222#yyy
toString:  https://hello:passwd@hello.com:80/jiji/haha?xxx=111&xxx=222#yyy
username:  hello
domainToASCII:  xn--fiq228c.com
domainToUnicode:  中文.com
fileURLToPath:  /home/h.txt
resolve:  dfdf/dfff/ddcdcd
urlToHttpOptions:  [Object: null prototype] {
  protocol: 'https:',
  hostname: 'hello.com',
  hash: '#yyy',
  search: '?xxx=111&xxx=222',
  pathname: '/jiji/haha',
  path: '/jiji/haha?xxx=111&xxx=222',
  href: 'https://hello:passwd@hello.com:80/jiji/haha?xxx=111&xxx=222#yyy',
  port: 80,
  auth: 'hello:passwd'
}
format:  https://hello.com:80/jiji/haha?xxx=111&xxx=222#yyy
pathToFileURL:  URL {
  href: 'file:///home/r.txt',
  origin: 'null',
  protocol: 'file:',
  username: '',
  password: '',
  host: '',
  hostname: '',
  port: '',
  pathname: '/home/r.txt',
  search: '',
  searchParams: URLSearchParams {},
  hash: ''
}
```



# 8、FS



```ts
import * as FS from 'fs/promises';

async function test(): Promise<void> {
    // cp ./stdout.log ./stdout.log1
    // await FS.cp('./stdout.log', './stdout.log1');
    // rm ./stderr.log
    // await FS.rm('./stderr.log');

    // FS.appendFile('./stdout.log1', 'haha');
    try {
        await FS.access('/etc/passwd', FS.constants.W_OK);
    }
    catch (error) {
        console.log('access: ', error);
    }
    // chmod 666 ./stdout.log
    // await FS.chmod('./stdout.log', '666');
    //
    // await FS.chown('./stdout.log', 0, 0);
    // await FS.copyFile('./stdout.log', './stdout.log2');
    // FS.lchown()

    // await FS.link('./stdout.log', './stdout.log3');

    const stat1 = await FS.lstat('./stdout.log3');
    console.log('stat1: ', stat1);

    await FS.lutimes('./stdout.log3', 1000, 1000);
    const stat2 = await FS.lstat('./stdout.log3');
    console.log('stat2: ', stat2);

    // mkdir hello
    // await FS.mkdir('hello');

    // 
    // await FS.mkdtemp('')

    // const data = await (await FS.open('./stdout.log')).read();
    // console.log('data: ', data);
    
    // const dir = await FS.opendir('./');
    // for await (const item of dir) {
    //     console.log(item.name);
        
    // }
    // console.log('dir: ', dir);

    // const data = await FS.readFile('./stdout.log');
    // console.log('data: ', data);

    // 
    // const data = await FS.readdir('./');
    // console.log('data: ', data);

    // const data = await FS.readlink('./stdout.log');
    // console.log('data: ', data);
    
    // const data = await FS.realpath('./');
    // console.log('data: ', data);
    
    // await FS.rename('./stdout.log3', './stdout.log4');

    // const stat = await FS.stat('./stdout.log');
    // console.log('stat: ', stat);

    // const statfs = await FS.statfs('./stdout.log');
    // console.log('statfs: ', statfs);
    
    // await FS.symlink('./stdout.log', './stdout.log4', 'file');

    // await FS.truncate('./stdout.log', 1024);

    // await FS.unlink('./stdout.log');

    // const watcher = await FS.watch('./stdout.log1');
    // for await (const item of watcher) {
    //     console.log(item.filename);
        
    // }

    await FS.writeFile('./stdout.log', 'hhhh');
}
(async () => {
    await test();
})();
```



# 9、Timers



```ts
const timeout = setTimeout(() => {
    console.log('timeout');
}, 1000);
const interval = setInterval(() => {
    console.log('setInterval');
}, 1000);
const immediate = setImmediate(() => {
    console.log('setImmediate');
});

clearTimeout(timeout);
clearInterval(interval);
clearImmediate(immediate);



```



# 10、Domian



```ts
import * as D from 'domain';

const myDomain = D.create();

myDomain.on('error', () => {
    console.log('error');
});

myDomain.run(() => {
    console.log('run');
    throw new Error('run error');
    
});

setInterval(() => {
    console.log('interval');
    
}, 1000);

```



# 11、Process



```ts
import * as P from 'process';
    
console.log('allowedNodeEnvironmentFlags: ', process.allowedNodeEnvironmentFlags);
console.log('arch: ', process.arch);
console.log('argv: ', process.argv);
console.log('argv0: ', process.argv0);
console.log('config: ', process.config);
console.log('connected: ', process.connected);
console.log('debugPort: ', process.debugPort);
console.log('env: ', process.env);
console.log('execArgv: ', process.execArgv);
console.log('execPath: ', process.execPath);
console.log('exitCode: ', process.exitCode);
console.log('features: ', process.features);
console.log('getegid: ', process.getegid());
console.log('geteuid: ', process.geteuid());
console.log('getgid: ', process.getgid());
console.log('getgroups: ', process.getgroups());
console.log('getuid: ', process.getuid());
console.log('hrtime: ', process.hrtime);
console.log('memoryUsage: ', process.memoryUsage);
console.log('pid: ', process.pid);
console.log('platform: ', process.platform);
console.log('ppid: ', process.ppid);
console.log('release: ', process.release);
console.log('report: ', process.report);
console.log('sourceMapsEnabled: ', process.sourceMapsEnabled);

console.log('title: ', process.title);
console.log('traceDeprecation: ', process.traceDeprecation);
console.log('version: ', process.version);
console.log('versions: ', process.versions);

//
allowedNodeEnvironmentFlags:  NodeEnvironmentFlagsSet(0) [Set] {
  '--experimental-abortcontroller',
  '--experimental-import-meta-resolve',
  '--no-experimental-import-meta-resolve',
  '--experimental-vm-modules',
  '--no-experimental-vm-modules',
  '--throw-deprecation',
  '--no-throw-deprecation',
  '--trace-deprecation',
  '--no-trace-deprecation',
  '--watch',
  '--no-watch',
  '--watch-path',
  '--require',
  '--napi-modules',
  '--extra-info-on-fatal-exception',
  '--no-extra-info-on-fatal-exception',
  '--stack-trace-limit',
  '--experimental-repl-await',
  '--no-experimental-repl-await',
  '--allow-child-process',
  '--no-allow-child-process',
  '--watch-preserve-output',
  '--no-watch-preserve-output',
  '--insecure-http-parser',
  '--no-insecure-http-parser',
  '--tls-min-v1.0',
  '--no-tls-min-v1.0',
  '--deprecation',
  '--no-deprecation',
  '--force-async-hooks-checks',
  '--no-force-async-hooks-checks',
  '--diagnostic-dir',
  '--report-on-fatalerror',
  '--no-report-on-fatalerror',
  '--warnings',
  '--no-warnings',
  '--tls-min-v1.1',
  '--no-tls-min-v1.1',
  '--pending-deprecation',
  '--no-pending-deprecation',
  '--tls-min-v1.2',
  '--no-tls-min-v1.2',
  '--preserve-symlinks-main',
  '--no-preserve-symlinks-main',
  '--allow-fs-write',
  '--global-search-paths',
  '--no-global-search-paths',
  '--experimental-global-customevent',
  '--no-experimental-global-customevent',
  '--node-memory-debug',
  '--experimental-specifier-resolution',
  '--addons',
  '--no-addons',
  '--trace-warnings',
  '--no-trace-warnings',
  '--experimental-json-modules',
  '--openssl-shared-config',
  '--no-openssl-shared-config',
  '--disable-proto',
  '--zero-fill-buffers',
  '--no-zero-fill-buffers',
  '--test-shard',
  '--experimental-worker',
  '--use-bundled-ca',
  '--no-use-bundled-ca',
  '--experimental-top-level-await',
  '--openssl-legacy-provider',
  '--no-openssl-legacy-provider',
  '--v8-pool-size',
  '--force-fips',
  '--no-force-fips',
  '--experimental-shadow-realm',
  '--no-experimental-shadow-realm',
  '--perf-prof-unwinding-info',
  '--force-context-aware',
  '--no-force-context-aware',
  '--max-semi-space-size',
  '--experimental-global-webcrypto',
  '--no-experimental-global-webcrypto',
  '--preserve-symlinks',
  '--no-preserve-symlinks',
  '--secure-heap-min',
  '--abort-on-uncaught-exception',
  '--trace-event-categories',
  '--report-dir',
  '--heapsnapshot-near-heap-limit',
  '--unhandled-rejections',
  '--snapshot-blob',
  '--huge-max-old-generation-size',
  '--node-snapshot',
  '--no-node-snapshot',
  '--icu-data-dir',
  '--trace-uncaught',
  '--no-trace-uncaught',
  '--openssl-config',
  '--report-filename',
  '--experimental-permission',
  '--no-experimental-permission',
  '--interpreted-frames-native-stack',
  '--tls-cipher-list',
  ... 97 more items,
  [Symbol(internal properties)]: {
    array: [
      '--experimental-abortcontroller',
      '--experimental-import-meta-resolve',
      '--no-experimental-import-meta-resolve',
      '--experimental-vm-modules',
      '--no-experimental-vm-modules',
      '--throw-deprecation',
      '--no-throw-deprecation',
      '--trace-deprecation',
      '--no-trace-deprecation',
      '--watch',
      '--no-watch',
      '--watch-path',
      '--require',
      '--napi-modules',
      '--extra-info-on-fatal-exception',
      '--no-extra-info-on-fatal-exception',
      '--stack-trace-limit',
      '--experimental-repl-await',
      '--no-experimental-repl-await',
      '--allow-child-process',
      '--no-allow-child-process',
      '--watch-preserve-output',
      '--no-watch-preserve-output',
      '--insecure-http-parser',
      '--no-insecure-http-parser',
      '--tls-min-v1.0',
      '--no-tls-min-v1.0',
      '--deprecation',
      '--no-deprecation',
      '--force-async-hooks-checks',
      '--no-force-async-hooks-checks',
      '--diagnostic-dir',
      '--report-on-fatalerror',
      '--no-report-on-fatalerror',
      '--warnings',
      '--no-warnings',
      '--tls-min-v1.1',
      '--no-tls-min-v1.1',
      '--pending-deprecation',
      '--no-pending-deprecation',
      '--tls-min-v1.2',
      '--no-tls-min-v1.2',
      '--preserve-symlinks-main',
      '--no-preserve-symlinks-main',
      '--allow-fs-write',
      '--global-search-paths',
      '--no-global-search-paths',
      '--experimental-global-customevent',
      '--no-experimental-global-customevent',
      '--node-memory-debug',
      '--experimental-specifier-resolution',
      '--addons',
      '--no-addons',
      '--trace-warnings',
      '--no-trace-warnings',
      '--experimental-json-modules',
      '--openssl-shared-config',
      '--no-openssl-shared-config',
      '--disable-proto',
      '--zero-fill-buffers',
      '--no-zero-fill-buffers',
      '--test-shard',
      '--experimental-worker',
      '--use-bundled-ca',
      '--no-use-bundled-ca',
      '--experimental-top-level-await',
      '--openssl-legacy-provider',
      '--no-openssl-legacy-provider',
      '--v8-pool-size',
      '--force-fips',
      '--no-force-fips',
      '--experimental-shadow-realm',
      '--no-experimental-shadow-realm',
      '--perf-prof-unwinding-info',
      '--force-context-aware',
      '--no-force-context-aware',
      '--max-semi-space-size',
      '--experimental-global-webcrypto',
      '--no-experimental-global-webcrypto',
      '--preserve-symlinks',
      '--no-preserve-symlinks',
      '--secure-heap-min',
      '--abort-on-uncaught-exception',
      '--trace-event-categories',
      '--report-dir',
      '--heapsnapshot-near-heap-limit',
      '--unhandled-rejections',
      '--snapshot-blob',
      '--huge-max-old-generation-size',
      '--node-snapshot',
      '--no-node-snapshot',
      '--icu-data-dir',
      '--trace-uncaught',
      '--no-trace-uncaught',
      '--openssl-config',
      '--report-filename',
      '--experimental-permission',
      '--no-experimental-permission',
      '--interpreted-frames-native-stack',
      '--tls-cipher-list',
      ... 97 more items
    ],
    set: Set(195) {
      '--experimental-abortcontroller',
      '--experimental-import-meta-resolve',
      '--no-experimental-import-meta-resolve',
      '--experimental-vm-modules',
      '--no-experimental-vm-modules',
      '--throw-deprecation',
      '--no-throw-deprecation',
      '--trace-deprecation',
      '--no-trace-deprecation',
      '--watch',
      '--no-watch',
      '--watch-path',
      '--require',
      '--napi-modules',
      '--extra-info-on-fatal-exception',
      '--no-extra-info-on-fatal-exception',
      '--stack-trace-limit',
      '--experimental-repl-await',
      '--no-experimental-repl-await',
      '--allow-child-process',
      '--no-allow-child-process',
      '--watch-preserve-output',
      '--no-watch-preserve-output',
      '--insecure-http-parser',
      '--no-insecure-http-parser',
      '--tls-min-v1.0',
      '--no-tls-min-v1.0',
      '--deprecation',
      '--no-deprecation',
      '--force-async-hooks-checks',
      '--no-force-async-hooks-checks',
      '--diagnostic-dir',
      '--report-on-fatalerror',
      '--no-report-on-fatalerror',
      '--warnings',
      '--no-warnings',
      '--tls-min-v1.1',
      '--no-tls-min-v1.1',
      '--pending-deprecation',
      '--no-pending-deprecation',
      '--tls-min-v1.2',
      '--no-tls-min-v1.2',
      '--preserve-symlinks-main',
      '--no-preserve-symlinks-main',
      '--allow-fs-write',
      '--global-search-paths',
      '--no-global-search-paths',
      '--experimental-global-customevent',
      '--no-experimental-global-customevent',
      '--node-memory-debug',
      '--experimental-specifier-resolution',
      '--addons',
      '--no-addons',
      '--trace-warnings',
      '--no-trace-warnings',
      '--experimental-json-modules',
      '--openssl-shared-config',
      '--no-openssl-shared-config',
      '--disable-proto',
      '--zero-fill-buffers',
      '--no-zero-fill-buffers',
      '--test-shard',
      '--experimental-worker',
      '--use-bundled-ca',
      '--no-use-bundled-ca',
      '--experimental-top-level-await',
      '--openssl-legacy-provider',
      '--no-openssl-legacy-provider',
      '--v8-pool-size',
      '--force-fips',
      '--no-force-fips',
      '--experimental-shadow-realm',
      '--no-experimental-shadow-realm',
      '--perf-prof-unwinding-info',
      '--force-context-aware',
      '--no-force-context-aware',
      '--max-semi-space-size',
      '--experimental-global-webcrypto',
      '--no-experimental-global-webcrypto',
      '--preserve-symlinks',
      '--no-preserve-symlinks',
      '--secure-heap-min',
      '--abort-on-uncaught-exception',
      '--trace-event-categories',
      '--report-dir',
      '--heapsnapshot-near-heap-limit',
      '--unhandled-rejections',
      '--snapshot-blob',
      '--huge-max-old-generation-size',
      '--node-snapshot',
      '--no-node-snapshot',
      '--icu-data-dir',
      '--trace-uncaught',
      '--no-trace-uncaught',
      '--openssl-config',
      '--report-filename',
      '--experimental-permission',
      '--no-experimental-permission',
      '--interpreted-frames-native-stack',
      '--tls-cipher-list',
      ... 95 more items
    }
  }
}
arch:  x64
argv:  [
  '/home/hello/.nvm/versions/node/v20.9.0/bin/node',
  '/home/hello/code/node/node-demos/app/process/process.js'
]
argv0:  node
config:  {
  target_defaults: {
    cflags: [],
    default_configuration: 'Release',
    defines: [
      'NODE_OPENSSL_CONF_NAME=nodejs_conf',
      'NODE_OPENSSL_HAS_QUIC',
      'ICU_NO_USER_DATA_OVERRIDE'
    ],
    include_dirs: [],
    libraries: []
  },
  variables: {
    asan: 0,
    coverage: false,
    dcheck_always_on: 0,
    debug_nghttp2: false,
    debug_node: false,
    enable_lto: false,
    enable_pgo_generate: false,
    enable_pgo_use: false,
    error_on_warn: false,
    force_dynamic_crt: 0,
    gas_version: '2.35',
    host_arch: 'x64',
    icu_data_in: '../../deps/icu-tmp/icudt73l.dat',
    icu_endianness: 'l',
    icu_gyp_path: 'tools/icu/icu-generic.gyp',
    icu_path: 'deps/icu-small',
    icu_small: false,
    icu_ver_major: '73',
    is_debug: 0,
    libdir: 'lib',
    llvm_version: '0.0',
    napi_build_version: '9',
    node_builtin_shareable_builtins: [
      'deps/cjs-module-lexer/lexer.js',
      'deps/cjs-module-lexer/dist/lexer.js',
      'deps/undici/undici.js'
    ],
    node_byteorder: 'little',
    node_debug_lib: false,
    node_enable_d8: false,
    node_enable_v8_vtunejit: false,
    node_fipsinstall: false,
    node_install_corepack: true,
    node_install_npm: true,
    node_library_files: [
      'lib/_http_agent.js',
      'lib/_http_client.js',
      'lib/_http_common.js',
      'lib/_http_incoming.js',
      'lib/_http_outgoing.js',
      'lib/_http_server.js',
      'lib/_stream_duplex.js',
      'lib/_stream_passthrough.js',
      'lib/_stream_readable.js',
      'lib/_stream_transform.js',
      'lib/_stream_wrap.js',
      'lib/_stream_writable.js',
      'lib/_tls_common.js',
      'lib/_tls_wrap.js',
      'lib/assert.js',
      'lib/assert/strict.js',
      'lib/async_hooks.js',
      'lib/buffer.js',
      'lib/child_process.js',
      'lib/cluster.js',
      'lib/console.js',
      'lib/constants.js',
      'lib/crypto.js',
      'lib/dgram.js',
      'lib/diagnostics_channel.js',
      'lib/dns.js',
      'lib/dns/promises.js',
      'lib/domain.js',
      'lib/events.js',
      'lib/fs.js',
      'lib/fs/promises.js',
      'lib/http.js',
      'lib/http2.js',
      'lib/https.js',
      'lib/inspector.js',
      'lib/inspector/promises.js',
      'lib/internal/abort_controller.js',
      'lib/internal/assert.js',
      'lib/internal/assert/assertion_error.js',
      'lib/internal/assert/calltracker.js',
      'lib/internal/async_hooks.js',
      'lib/internal/blob.js',
      'lib/internal/blocklist.js',
      'lib/internal/bootstrap/node.js',
      'lib/internal/bootstrap/realm.js',
      'lib/internal/bootstrap/switches/does_not_own_process_state.js',
      'lib/internal/bootstrap/switches/does_own_process_state.js',
      'lib/internal/bootstrap/switches/is_main_thread.js',
      'lib/internal/bootstrap/switches/is_not_main_thread.js',
      'lib/internal/bootstrap/web/exposed-wildcard.js',
      'lib/internal/bootstrap/web/exposed-window-or-worker.js',
      'lib/internal/buffer.js',
      'lib/internal/child_process.js',
      'lib/internal/child_process/serialization.js',
      'lib/internal/cli_table.js',
      'lib/internal/cluster/child.js',
      'lib/internal/cluster/primary.js',
      'lib/internal/cluster/round_robin_handle.js',
      'lib/internal/cluster/shared_handle.js',
      'lib/internal/cluster/utils.js',
      'lib/internal/cluster/worker.js',
      'lib/internal/console/constructor.js',
      'lib/internal/console/global.js',
      'lib/internal/constants.js',
      'lib/internal/crypto/aes.js',
      'lib/internal/crypto/certificate.js',
      'lib/internal/crypto/cfrg.js',
      'lib/internal/crypto/cipher.js',
      'lib/internal/crypto/diffiehellman.js',
      'lib/internal/crypto/ec.js',
      'lib/internal/crypto/hash.js',
      'lib/internal/crypto/hashnames.js',
      'lib/internal/crypto/hkdf.js',
      'lib/internal/crypto/keygen.js',
      'lib/internal/crypto/keys.js',
      'lib/internal/crypto/mac.js',
      'lib/internal/crypto/pbkdf2.js',
      'lib/internal/crypto/random.js',
      'lib/internal/crypto/rsa.js',
      'lib/internal/crypto/scrypt.js',
      'lib/internal/crypto/sig.js',
      'lib/internal/crypto/util.js',
      'lib/internal/crypto/webcrypto.js',
      'lib/internal/crypto/webidl.js',
      'lib/internal/crypto/x509.js',
      'lib/internal/debugger/inspect.js',
      'lib/internal/debugger/inspect_client.js',
      'lib/internal/debugger/inspect_repl.js',
      'lib/internal/dgram.js',
      'lib/internal/dns/callback_resolver.js',
      'lib/internal/dns/promises.js',
      'lib/internal/dns/utils.js',
      'lib/internal/encoding.js',
      'lib/internal/error_serdes.js',
      'lib/internal/errors.js',
      'lib/internal/event_target.js',
      'lib/internal/events/symbols.js',
      'lib/internal/file.js',
      'lib/internal/fixed_queue.js',
      'lib/internal/freelist.js',
      ... 214 more items
    ],
    node_module_version: 115,
    node_no_browser_globals: false,
    node_prefix: '/',
    node_release_urlbase: 'https://nodejs.org/download/release/',
    node_section_ordering_info: '',
    node_shared: false,
    node_shared_brotli: false,
    node_shared_cares: false,
    node_shared_http_parser: false,
    node_shared_libuv: false,
    node_shared_nghttp2: false,
    node_shared_nghttp3: false,
    node_shared_ngtcp2: false,
    node_shared_openssl: false,
    node_shared_zlib: false,
    node_tag: '',
    node_target_type: 'executable',
    node_use_bundled_v8: true,
    node_use_node_code_cache: true,
    node_use_node_snapshot: true,
    node_use_openssl: true,
    node_use_v8_platform: true,
    node_with_ltcg: false,
    node_without_node_options: false,
    node_write_snapshot_as_array_literals: false,
    openssl_is_fips: false,
    openssl_quic: true,
    ossfuzz: false,
    shlib_suffix: 'so.115',
    single_executable_application: true,
    target_arch: 'x64',
    v8_enable_31bit_smis_on_64bit_arch: 0,
    v8_enable_gdbjit: 0,
    v8_enable_hugepage: 0,
    v8_enable_i18n_support: 1,
    v8_enable_inspector: 1,
    v8_enable_javascript_promise_hooks: 1,
    v8_enable_lite_mode: 0,
    v8_enable_object_print: 1,
    v8_enable_pointer_compression: 0,
    v8_enable_shared_ro_heap: 1,
    v8_enable_short_builtin_calls: 1,
    v8_enable_webassembly: 1,
    v8_no_strict_aliasing: 1,
    v8_optimized_debug: 1,
    v8_promise_internal_field_count: 1,
    v8_random_seed: 0,
    v8_trace_maps: 0,
    v8_use_siphash: 1,
    want_separate_host_toolset: 0
  }
}
connected:  undefined
debugPort:  9229
env:  {
  USER: 'hello',
  SSH_CLIENT: '192.168.43.50 55405 22',
  XDG_SESSION_TYPE: 'tty',
  SHLVL: '2',
  MOTD_SHOWN: 'pam',
  HOME: '/home/hello',
  OLDPWD: '/home/hello/code/node/node-demos',
  DBUS_SESSION_BUS_ADDRESS: 'unix:path=/run/user/1000/bus',
  LOGNAME: 'hello',
  _: '/home/hello/.nvm/versions/node/v20.9.0/bin/node',
  XDG_SESSION_CLASS: 'user',
  XDG_SESSION_ID: '79',
  PATH: '/home/hello/.nvm/versions/node/v20.9.0/bin:/home/hello/.vscode-server/bin/1a5daa3a0231a0fbba4f14db7ec463cf99d7768e/bin/remote-cli:/home/hello/.cargo/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin',
  XDG_RUNTIME_DIR: '/run/user/1000',
  LANG: 'en_US.UTF-8',
  SHELL: '/usr/bin/zsh',
  PWD: '/home/hello/code/node/node-demos',
  SSH_CONNECTION: '192.168.43.50 55405 192.168.43.51 22',
  BROWSER: '/home/hello/.vscode-server/bin/1a5daa3a0231a0fbba4f14db7ec463cf99d7768e/bin/helpers/browser.sh',
  TERM_PROGRAM: 'vscode',
  TERM_PROGRAM_VERSION: '1.84.2',
  COLORTERM: 'truecolor',
  GIT_ASKPASS: '/home/hello/.vscode-server/bin/1a5daa3a0231a0fbba4f14db7ec463cf99d7768e/extensions/git/dist/askpass.sh',
  VSCODE_GIT_ASKPASS_NODE: '/home/hello/.vscode-server/bin/1a5daa3a0231a0fbba4f14db7ec463cf99d7768e/node',
  VSCODE_GIT_ASKPASS_EXTRA_ARGS: '',
  VSCODE_GIT_ASKPASS_MAIN: '/home/hello/.vscode-server/bin/1a5daa3a0231a0fbba4f14db7ec463cf99d7768e/extensions/git/dist/askpass-main.js',
  VSCODE_GIT_IPC_HANDLE: '/run/user/1000/vscode-git-c6fa870fc2.sock',
  VSCODE_IPC_HOOK_CLI: '/run/user/1000/vscode-ipc-edce455f-a487-4cf3-9020-288c90bd4244.sock',
  VSCODE_INJECTION: '1',
  VSCODE_NONCE: '2788ed63-076f-4d31-be51-9ecb8beb10e5',
  ZDOTDIR: '/home/hello',
  USER_ZDOTDIR: '/home/hello',
  TERM: 'xterm-256color',
  ZSH: '/home/hello/.oh-my-zsh',
  PAGER: 'less',
  LESS: '-R',
  LSCOLORS: 'Gxfxcxdxbxegedabagacad',
  LS_COLORS: 'rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:',
  P9K_SSH: '1',
  _P9K_SSH_TTY: '/dev/pts/0',
  NVM_DIR: '/home/hello/.nvm',
  NVM_CD_FLAGS: '-q',
  NVM_BIN: '/home/hello/.nvm/versions/node/v20.9.0/bin',
  NVM_INC: '/home/hello/.nvm/versions/node/v20.9.0/include/node',
  P9K_TTY: 'old',
  _P9K_TTY: '/dev/pts/0'
}
execArgv:  []
execPath:  /home/hello/.nvm/versions/node/v20.9.0/bin/node
exitCode:  undefined
features:  {
  inspector: true,
  debug: false,
  uv: true,
  ipv6: true,
  tls_alpn: true,
  tls_sni: true,
  tls_ocsp: true,
  tls: true,
  cached_builtins: [Getter]
}
getegid:  1000
geteuid:  1000
getgid:  1000
getgroups:  [
    4,   24,  27,
   30,   46, 116,
  998, 1000
]
getuid:  1000
hrtime:  [Function: hrtime] { bigint: [Function: hrtimeBigInt] }
memoryUsage:  [Function: memoryUsage] { rss: [Function: rss] }
pid:  197196
platform:  linux
ppid:  183857
release:  {
  name: 'node',
  lts: 'Iron',
  sourceUrl: 'https://nodejs.org/download/release/v20.9.0/node-v20.9.0.tar.gz',
  headersUrl: 'https://nodejs.org/download/release/v20.9.0/node-v20.9.0-headers.tar.gz'
}
report:  {
  writeReport: [Function: writeReport],
  getReport: [Function: getReport],
  directory: [Getter/Setter],
  filename: [Getter/Setter],
  compact: [Getter/Setter],
  signal: [Getter/Setter],
  reportOnFatalError: [Getter/Setter],
  reportOnSignal: [Getter/Setter],
  reportOnUncaughtException: [Getter/Setter]
}
sourceMapsEnabled:  false
title:  node
traceDeprecation:  undefined
version:  v20.9.0
versions:  {
  node: '20.9.0',
  acorn: '8.10.0',
  ada: '2.6.0',
  ares: '1.19.1',
  base64: '0.5.0',
  brotli: '1.0.9',
  cjs_module_lexer: '1.2.2',
  cldr: '43.1',
  icu: '73.2',
  llhttp: '8.1.1',
  modules: '115',
  napi: '9',
  nghttp2: '1.57.0',
  nghttp3: '0.7.0',
  ngtcp2: '0.8.1',
  openssl: '3.0.10+quic',
  simdutf: '3.2.17',
  tz: '2023c',
  undici: '5.26.3',
  unicode: '15.0',
  uv: '1.46.0',
  uvwasi: '0.0.18',
  v8: '11.3.244.8-node.16',
  zlib: '1.2.13.1-motley'
}

```



# 12、Stream



```ts
import * as FS from 'fs';
import * as S from 'stream';

const input = FS.createReadStream('./stderr.log');
const output = FS.createWriteStream('./stdout.log');
const tran = new S.Transform({
    transform(chunk, encoding, callback) {
        console.log(chunk.toString('utf-8'));
        callback(null, Buffer.from(chunk.toString('utf-8').toUpperCase()));
    }
});
const p = S.pipeline(
    input,
    tran,
    output,
    (err) => {
        if (err) {
            console.log(err);
            
        }
        else {
            console.log('pipeline ok');
            
        }
    }
);
setInterval(() => {}, 1000)


const writeStream = FS.createWriteStream('./stderr.log');
writeStream.on('drain', () => {
    console.log('drain');
});
while (true) {
    const flag = writeStream.write('hhhhhhhhchhhhhhhhhhh');
    if (!flag) {
        console.log('flag: ', flag);
        break;
    }
}


```

# 13、Buffer

```ts
import { log } from "console";

const buf1 = Buffer.from('hello', 'base64');
console.log('buf1', buf1);

for (const [key, value] of buf1.entries()) {
    console.log('key: ', key, 'value: ', value);
}

for (const key of buf1.keys()) {
    console.log('key: ', key);
}
```



# 14、Assert

```ts
import * as Assert from 'assert';

const err = new Error('error');
// Assert.ifError(err);

// Assert.ok(false);

// Assert.equal(2, 1+1);
// Assert.equal(3, 4, 'not equal');

// Assert.fail('jjjj');

// Assert.deepEqual([[[1, 2, 3]], 4, 5], [[[1, 2, '3']], 4, 5]);
// Assert.deepStrictEqual([[[1, 2, 3]], 4, 5], [[[1, 2, '3']], 4, 5]);

// Assert.match('112a', /^\d*$/);
// Assert.doesNotMatch('112', /^\d*$/);

// Assert.strictEqual('', false);
// Assert.equal('', false);


// Assert.throws(() => {
//     throw new Error('error');
// });

// Assert.doesNotThrow(() => {
//     throw new Error('error');
// });

// Assert.rejects(async () => {
//     throw new Error('error');
// });

// Assert.doesNotReject(async () => {
//     throw new Error('error');
// });

```



# 15、QueryString



```ts
import * as QS from 'querystring';

const str = 'a=123&a=333&b=222';
const data = QS.parse(str);
console.log(JSON.stringify(data, undefined, '    '));


const obj = {
    k1: 1111,
    k2: [
        222,333
    ]
};
console.log(QS.encode(obj));
const data1 = QS.escape(str);
console.log('data1: ', data1);

console.log('data2: ', QS.unescape(data1));

//
{
    "a": [
        "123",
        "333"
    ],
    "b": "222"
}
k1=1111&k2=222&k2=333
data1:  a%3D123%26a%3D333%26b%3D222
data2:  a=123&a=333&b=222
```



# 16、StringDecoder

```ts
import * as SD from 'string_decoder';

const strBuf = Buffer.from('你好', 'utf8');
const decoder = new SD.StringDecoder('utf8');

for (const data of strBuf) {
    console.log(data);
    const result = decoder.write(Buffer.from([data]));
    console.log(result);
}


```



# 17、Readline



```ts
import * as Readline from 'readline';

const rl = Readline.createInterface(process.stdin, process.stdout);
rl.on('close', () => {
    console.log('close');
    
});
async function askQuestion(rl: Readline.Interface, question: string): Promise<string> {
    return new Promise((resolve) => {
        rl.question(question, (answer) => {
            resolve(answer);
        });
    })
}

(async () => {
    for (let i = 0; i < 3; i++) {
        const answer = await askQuestion(rl, `question-${i}: `);
        console.log(`answer-${i}: `, answer);
        
    }
    rl.close();
})();

//
question-0: 1
answer-0:  1
question-1: 2
answer-1:  2
question-2: 2
answer-2:  2
close
```



# 18、Punycode

```ts
import * as Punycode from 'punycode';

console.log(Punycode.version);

const str1 = Punycode.encode('你好123');
console.log('str1: ', str1);
console.log('str2: ', Punycode.decode(str1));

console.log(Punycode.toASCII('你好123'));
console.log(Punycode.toUnicode(str1));
console.log(Punycode.ucs2);

//
2.1.0
str1:  123-x69dj67b
str2:  你好123
xn--123-x69dj67b
123-x69dj67b
{ decode: [Function: ucs2decode], encode: [Function: ucs2encode] }
```



# 19、ChildProcess



```ts
// childProcess.ts
import * as P from 'child_process';

// 
// const process1 = P.spawn('ls', ['-h']);
// process1.on('close', (code) => {
//     console.log('close: ', code);
// });
// process1.on('exit', (code) => {
//     console.log('exit: ', code);
// });

// process1.on('error', (err) => {
//     console.log('error: ', err);
// });

// process1.on('spawn', () => {
//     console.log('spawn');
// });

// process1.on('disconnect', () => {
//     console.log('disconnect');
// });

// process1.on('message', (message, handler) => {
//     console.log('message: ', message.toString());
// })

// process1.stdout.on('data', (chunk) => {
//     console.log('stdout: ', chunk.toString('utf8'));
// });

// process1.stderr.on('data', (chunk) => {
//     console.log('stderr: ', chunk.toString('utf8'));
    
// });
//


// const p2 = P.execFile('ls', ['-h']);
// p2.stdout?.on('data', (chunk) => {
//     console.log('data: ', chunk.toString());
    
// });

// const p3 = P.execFileSync('ls', ['-h']);
// console.log('p3: ', p3.toString());


const p4 = P.exec('ls');
p4.stdout?.on('data', (chunk) => {
    console.log('data: ', chunk.toString()); 
});


// pub.ts
import * as P from 'child_process';
const p1 = P.fork(`${__dirname}/sub.js`);
p1.send('haha');

p1.on('message', (message, handler) => {
    console.log('message: ', message);
    
})

p1.on('close', (code) => {
    console.log('close: ', code);
    
});

p1.on('error', (code) => {
    console.log('error: ', code);
    
});

p1.on('exit', (code) => {
    console.log('exit: ', code);
});

p1.on('disconnect', () => {
    console.log('disconnect');
});

p1.on('spawn', () => {
    console.log('spawn');
});

p1.send('hhhhh');

// sub.ts
process.on('message', (message, handler) => {
    console.log('message: ', message);
});

process.send('hello' + Date.now());

setTimeout(() => {
    process.disconnect();
}, 1000);
```

# 20、Cluster



```ts
import * as C from 'cluster';
import * as OS from 'os';

console.log('isPrimary: ', C.default.isPrimary);
console.log('isWorker', C.default.isWorker);

// console.log('settings: ', C.default.settings);
// console.log('worker: ', C.default.worker);
if (C.default.isPrimary) {
    for (let i = 0; i < OS.cpus.length;i++) {
        C.default.fork();
    }
}
const interval = setInterval(() => {
    console.log('setInterval');
}, 0);

```



# 21、HTTP

```ts
// server.ts
import * as Http from 'http';
import * as U from 'url';

const server = Http.createServer((req, res) => {
    //
    const method = req.method;
    const u = U.parse(req.url ?? '', true);
    const pathname = u.pathname;
    console.log('method: ', method);
    console.log('pathname: ', pathname);

    if (method === 'GET' && pathname === '/hello') {
        res.write(JSON.stringify({
            message: 'OK'
        }));
        res.statusCode = 303;
        res.end();
    }
    
    
});

server.listen(
    10000,
    '192.168.43.51',
    () => {
        console.log('server start');
    }
);

// client.ts
import { request } from 'http';

const options = {
  hostname: '192.168.43.51',
  port: 10000,
  method: 'GET',
  path: '/hello', // Correct property name is 'path' instead of 'pathname'
};

const client = request(options, (response) => {
  let body = '';

  response.on('data', (chunk) => {
    body += chunk;
  });

  response.on('end', () => {
    console.log(body);
  });
});

client.on('error', (error) => {
  console.error(`Error: ${error.message}`);
});

client.end(); 
```



# 22、HTTP2



```ts
// server.ts

import * as Http2 from 'http2';
import * as URL from 'url';

const server = Http2.createServer((req, res) => {
    const method = req.method;
    const u = URL.parse(req.url, true);
    const key = u.query['key'];
    const headers = req.headers;
    const pathname = u.pathname;

    console.log('key: ', key);
    console.log('headers: ', headers);
    console.log('pathname: ', pathname);
    console.log('method: ', method);

    if (method === 'GET' && pathname === '/hello') {
        res.write(JSON.stringify({
            date: Date.now()
        }));
        res.statusCode = 250;
        res.end();
    }
});
server.listen(
    10000,
    '192.168.43.51',
    () => {
        console.log('start server');     
    }
);

// client.ts

import * as http2 from 'http2';

const options = {
  host: '192.168.43.51',
  port: 10000,
  path: '/hello?key=myKey', // Adjust the key parameter as needed
  method: 'GET',
};

const client = http2.connect('http://192.168.43.51:10000');
const req = client.request({
    ":method": "GET",
    ":path": '/hello',
    "content-type": "application/json",
});


let data = "";

req.on("response", (headers, flags) => {
 for (const name in headers) {
  console.log(`${name}: ${headers[name]}`);
 }

});

req.on("data", chunk => {
 data += chunk;
});
req.on("end", () => {
 console.log(data);
 client.close();
});
req.end();

```



# 23、TCP



```ts
// server.ts

import * as Net from 'net';

const server = Net.createServer((socket) => {
    socket.on('connect', () => {
        console.log('connect');
        
    });

    socket.on('data', (data) => {
        console.log('data: ', data.toString('utf8'));
        socket.write('world')
    });

    socket.on('close', (error) => {
        console.log('close: ', error);
        
    });

    socket.on('end', () => {
        console.log('end');
    });

    socket.on('error', (err) => {
        console.log('error: ', err);
    });
});
server.listen(
    10000,
    '192.168.43.51',
    () => {
        console.log('start server');
    }
);


// client.ts

import * as Net from 'net';

const client = Net.connect({
    host: '192.168.43.51',
    port: 10000
});

client.on('connect', () => {
    console.log('connect');
    
});
client.on('data', (data) => {
    console.log('data: ', data.toString('utf8'));
    
});

client.on('error', (err) => {
    console.log('error: ', err);
});


client.on('end', () => {
    console.log('end');
    
});
client.on('close', (hadError) => {
    console.log('close: ', hadError);
    
});
client.write('hello');


```



# 24、UDP



```ts
// server.ts

import * as D from 'dgram';

const server = D.createSocket('udp4');

server.on('close', () => {
    console.log('close');
    
});

server.on('connect', () => {
    console.log('connect');
    
});

server.on('error', (error) => {
    console.log('error: ', error);
    
});

server.on('listening', () => {
    console.log('listening');
    
});

server.on('message', (msg, rInfo) => {
    console.log('msg: ', msg.toString('utf8'), ' rInfo: ', JSON.stringify(rInfo)); 
    server.send('world', rInfo.port, rInfo.address);
});
server.bind(10000, '192.168.43.51');


//client.ts

import * as D from 'dgram';

const client = D.createSocket('udp4');

client.on('close', () => {
    console.log('close');
    
});

client.on('connect', () => {
    console.log('connect');
    
});

client.on('error', (error) => {
    console.log('error: ', error);
    
});

client.on('listening', () => {
    console.log('listening');
    
});

client.on('message', (msg, rInfo) => {
    console.log('msg: ', msg.toString('utf8'), ' rInfo: ', JSON.stringify(rInfo)); 
});

client.send(
    'hello',
    10000,
    '192.168.43.51'
);
```



# 25、UnixSocket



```ts
// server.ts

import * as Net from 'net';
import * as FS from 'fs';

const server = Net.createServer((socket) => {
    socket.on('connect', () => {
        console.log('connect');
        
    });

    socket.on('data', (data) => {
        console.log('data: ', data.toString('utf8'));
        socket.write('world')
    });

    socket.on('close', (error) => {
        console.log('close: ', error);
        
    });

    socket.on('end', () => {
        console.log('end');
    });

    socket.on('error', (err) => {
        console.log('error: ', err);
    });
});

const socketPath = './demo.sock';
if (FS.existsSync(socketPath)) {
    FS.rmSync(socketPath)
    console.log('rm: ', socketPath);
    
}

server.listen(
    socketPath,
    () => {
        console.log('start server');
        
    }
)


// client.ts

import * as Net from 'net';

const client = Net.connect({
    path: './demo.sock'
});

client.on('connect', () => {
    console.log('connect');
    
});
client.on('data', (data) => {
    console.log('data: ', data.toString('utf8'));
    
});

client.on('error', (err) => {
    console.log('error: ', err);
});


client.on('end', () => {
    console.log('end');
    
});
client.on('close', (hadError) => {
    console.log('close: ', hadError);
    
});
client.write('hello');



```



# 26、HTTPS



```ts
// server.ts

import * as Https from 'https';
import * as FS from 'fs';
import * as URL from 'url';

const cert = FS.readFileSync('./cert/server-cert.pem');
const key = FS.readFileSync('./cert/server-key.pem');

const server = Https.createServer(
    {
        cert,
        key,
        rejectUnauthorized: false
    },
    (req, res) => {
        const method = req.method;
        const u = URL.parse(req.url ?? '', true);
        const pathname = u.pathname;
        res.write(JSON.stringify({
            message: 'ok',
            method,
            pathname
        }));
        res.end();
    }
);

server.listen(
    10000,
    '192.168.43.51',
    () => {
        console.log('start server');
        
    }
)

// client.ts

import * as Https from 'https';
import * as FS from 'fs';
const cert = FS.readFileSync('./cert/server-cert.pem');
const req = Https.request(
    {
        hostname: '192.168.43.51',
        port: 10000,
        method: 'GET',
        pathname: '/hello',
        cert,
        ca: cert,
        rejectUnauthorized: false
    },
    (res) => {
        let data = '';
        res.on('data', (chunk) => {
            data += chunk.toString();
        });

        res.on('end', () => {
            console.log('data: ', data);
        });

        res.on('error', (err) => {
            console.log('error: ', err);
            
        })
    }
);

req.write('client hello');
req.end();
```





# 27、TLS



**cert**

```sh
# 生成私钥
openssl genpkey -algorithm RSA -out server-key.pem

# 生成CSR并添加Subject Alternative Name (SAN)
openssl req -new -key server-key.pem -out server-csr.pem \
  -subj "/CN=192.168.43.51" \
  -addext "subjectAltName = IP:192.168.43.51"

# 签名证书
openssl x509 -req -in server-csr.pem -signkey server-key.pem -out server-cert.pem


# 生成私钥
openssl genpkey -algorithm RSA -out client-key.pem

# 生成CSR并添加Subject Alternative Name (SAN)
openssl req -new -key client-key.pem -out client-csr.pem \
  -subj "/CN=192.168.43.51" \
  -addext "subjectAltName = IP:192.168.43.51"

# 签名证书
openssl x509 -req -in client-csr.pem -signkey client-key.pem -out client-cert.pem

```



```ts
// server.ts
import * as T from 'tls';
import * as FS from 'fs';

const cert = FS.readFileSync('./cert/server-cert.pem');
const key = FS.readFileSync('./cert/server-key.pem');

const server = T.createServer(
    {
        cert,
        key,
        ca: cert,
        rejectUnauthorized: false
    },
    (socket) => {
        socket.on('secureConnect', () => {
            console.log('secureConnect');
            
        });

        socket.on('OCSPResponse', (response) => {
            console.log('OCSPResponse: ', response.toString());
            
        });

        socket.on('keylog', (line) => {
            console.log('keyLog: ', line.toString());
            
        });

        socket.on('session', (session) => {
            console.log('session: ', session.toString());
            
        });


        socket.on('error', (err) => {
            console.log('error: ', err);
            
        });

        socket.on('data', (data) => {
            console.log('data: ', data.toString());
    
        });

        socket.on('end', () => {
            console.log('end');
            
        });

        socket.write('server: hello');
    }
)

server.listen(
    10000,
    '192.168.43.51',
    () => {
        console.log('start server');
        
    }
);



// client.ts

import * as T from 'tls';
import * as FS from 'fs';

const cert = FS.readFileSync('./cert/client-cert.pem');
const key = FS.readFileSync('./cert/client-key.pem');

const client = T.connect(
    10000,
    '192.168.43.51',
    {
        cert,
        key,
        ca: cert,
        rejectUnauthorized: false
    },
    () => {
        console.log('start client');
    }
);

client.on('secureConnect', () => {
    console.log('secureConnect');
    
});

client.on('OCSPResponse', (response) => {
    console.log('OCSPResponse: ', response.toString());
    
});

client.on('keylog', (line) => {
    console.log('keyLog: ', line.toString());
    
});

client.on('session', (session) => {
    console.log('session: ', session.toString());
    
});

client.on('error', (err) => {
    console.log('error: ', err);
    
});

client.on('data', (data) => {
    console.log('data: ', data.toString());

});
client.write('client: hello');


```





# 28、Crypto



**crypto**

```ts
import { log } from 'console';
import * as C from 'crypto';

console.log('checkPrimeSync: ', C.checkPrimeSync(5n));

console.log('generatePrimeSync: ', C.generatePrimeSync(100));

console.log('getHashes: ', C.getHashes());
console.log('getCiphers: ', C.getCiphers());
console.log('getCurves: ', C.getCurves());
console.log('getFips: ', C.getFips());
console.log('randomUUID: ', C.randomUUID());
console.log('randomBytes: ', C.randomBytes(16));


//
checkPrimeSync:  true
generatePrimeSync:  ArrayBuffer {
  [Uint8Contents]: <0d 77 19 26 2a e0 d9 99 50 a4 b4 8e 47>,
  byteLength: 13
}
getHashes:  [
  'RSA-MD5',
  'RSA-RIPEMD160',
  'RSA-SHA1',
  'RSA-SHA1-2',
  'RSA-SHA224',
  'RSA-SHA256',
  'RSA-SHA3-224',
  'RSA-SHA3-256',
  'RSA-SHA3-384',
  'RSA-SHA3-512',
  'RSA-SHA384',
  'RSA-SHA512',
  'RSA-SHA512/224',
  'RSA-SHA512/256',
  'RSA-SM3',
  'blake2b512',
  'blake2s256',
  'id-rsassa-pkcs1-v1_5-with-sha3-224',
  'id-rsassa-pkcs1-v1_5-with-sha3-256',
  'id-rsassa-pkcs1-v1_5-with-sha3-384',
  'id-rsassa-pkcs1-v1_5-with-sha3-512',
  'md5',
  'md5-sha1',
  'md5WithRSAEncryption',
  'ripemd',
  'ripemd160',
  'ripemd160WithRSA',
  'rmd160',
  'sha1',
  'sha1WithRSAEncryption',
  'sha224',
  'sha224WithRSAEncryption',
  'sha256',
  'sha256WithRSAEncryption',
  'sha3-224',
  'sha3-256',
  'sha3-384',
  'sha3-512',
  'sha384',
  'sha384WithRSAEncryption',
  'sha512',
  'sha512-224',
  'sha512-224WithRSAEncryption',
  'sha512-256',
  'sha512-256WithRSAEncryption',
  'sha512WithRSAEncryption',
  'shake128',
  'shake256',
  'sm3',
  'sm3WithRSAEncryption',
  'ssl3-md5',
  'ssl3-sha1'
]
getCiphers:  [
  'aes-128-cbc',
  'aes-128-cbc-hmac-sha1',
  'aes-128-cbc-hmac-sha256',
  'aes-128-ccm',
  'aes-128-cfb',
  'aes-128-cfb1',
  'aes-128-cfb8',
  'aes-128-ctr',
  'aes-128-ecb',
  'aes-128-gcm',
  'aes-128-ocb',
  'aes-128-ofb',
  'aes-128-xts',
  'aes-192-cbc',
  'aes-192-ccm',
  'aes-192-cfb',
  'aes-192-cfb1',
  'aes-192-cfb8',
  'aes-192-ctr',
  'aes-192-ecb',
  'aes-192-gcm',
  'aes-192-ocb',
  'aes-192-ofb',
  'aes-256-cbc',
  'aes-256-cbc-hmac-sha1',
  'aes-256-cbc-hmac-sha256',
  'aes-256-ccm',
  'aes-256-cfb',
  'aes-256-cfb1',
  'aes-256-cfb8',
  'aes-256-ctr',
  'aes-256-ecb',
  'aes-256-gcm',
  'aes-256-ocb',
  'aes-256-ofb',
  'aes-256-xts',
  'aes128',
  'aes128-wrap',
  'aes192',
  'aes192-wrap',
  'aes256',
  'aes256-wrap',
  'aria-128-cbc',
  'aria-128-ccm',
  'aria-128-cfb',
  'aria-128-cfb1',
  'aria-128-cfb8',
  'aria-128-ctr',
  'aria-128-ecb',
  'aria-128-gcm',
  'aria-128-ofb',
  'aria-192-cbc',
  'aria-192-ccm',
  'aria-192-cfb',
  'aria-192-cfb1',
  'aria-192-cfb8',
  'aria-192-ctr',
  'aria-192-ecb',
  'aria-192-gcm',
  'aria-192-ofb',
  'aria-256-cbc',
  'aria-256-ccm',
  'aria-256-cfb',
  'aria-256-cfb1',
  'aria-256-cfb8',
  'aria-256-ctr',
  'aria-256-ecb',
  'aria-256-gcm',
  'aria-256-ofb',
  'aria128',
  'aria192',
  'aria256',
  'camellia-128-cbc',
  'camellia-128-cfb',
  'camellia-128-cfb1',
  'camellia-128-cfb8',
  'camellia-128-ctr',
  'camellia-128-ecb',
  'camellia-128-ofb',
  'camellia-192-cbc',
  'camellia-192-cfb',
  'camellia-192-cfb1',
  'camellia-192-cfb8',
  'camellia-192-ctr',
  'camellia-192-ecb',
  'camellia-192-ofb',
  'camellia-256-cbc',
  'camellia-256-cfb',
  'camellia-256-cfb1',
  'camellia-256-cfb8',
  'camellia-256-ctr',
  'camellia-256-ecb',
  'camellia-256-ofb',
  'camellia128',
  'camellia192',
  'camellia256',
  'chacha20',
  'chacha20-poly1305',
  'des-ede',
  'des-ede-cbc',
  ... 31 more items
]
getCurves:  [
  'Oakley-EC2N-3',
  'Oakley-EC2N-4',
  'SM2',
  'brainpoolP160r1',
  'brainpoolP160t1',
  'brainpoolP192r1',
  'brainpoolP192t1',
  'brainpoolP224r1',
  'brainpoolP224t1',
  'brainpoolP256r1',
  'brainpoolP256t1',
  'brainpoolP320r1',
  'brainpoolP320t1',
  'brainpoolP384r1',
  'brainpoolP384t1',
  'brainpoolP512r1',
  'brainpoolP512t1',
  'c2pnb163v1',
  'c2pnb163v2',
  'c2pnb163v3',
  'c2pnb176v1',
  'c2pnb208w1',
  'c2pnb272w1',
  'c2pnb304w1',
  'c2pnb368w1',
  'c2tnb191v1',
  'c2tnb191v2',
  'c2tnb191v3',
  'c2tnb239v1',
  'c2tnb239v2',
  'c2tnb239v3',
  'c2tnb359v1',
  'c2tnb431r1',
  'prime192v1',
  'prime192v2',
  'prime192v3',
  'prime239v1',
  'prime239v2',
  'prime239v3',
  'prime256v1',
  'secp112r1',
  'secp112r2',
  'secp128r1',
  'secp128r2',
  'secp160k1',
  'secp160r1',
  'secp160r2',
  'secp192k1',
  'secp224k1',
  'secp224r1',
  'secp256k1',
  'secp384r1',
  'secp521r1',
  'sect113r1',
  'sect113r2',
  'sect131r1',
  'sect131r2',
  'sect163k1',
  'sect163r1',
  'sect163r2',
  'sect193r1',
  'sect193r2',
  'sect233k1',
  'sect233r1',
  'sect239k1',
  'sect283k1',
  'sect283r1',
  'sect409k1',
  'sect409r1',
  'sect571k1',
  'sect571r1',
  'wap-wsg-idm-ecid-wtls1',
  'wap-wsg-idm-ecid-wtls10',
  'wap-wsg-idm-ecid-wtls11',
  'wap-wsg-idm-ecid-wtls12',
  'wap-wsg-idm-ecid-wtls3',
  'wap-wsg-idm-ecid-wtls4',
  'wap-wsg-idm-ecid-wtls5',
  'wap-wsg-idm-ecid-wtls6',
  'wap-wsg-idm-ecid-wtls7',
  'wap-wsg-idm-ecid-wtls8',
  'wap-wsg-idm-ecid-wtls9'
]
getFips:  0
: 
: 
: 
: 
: 
: 
: 
: 
: 
: 
: 
~/code/node/node-demos ❯ npm rub build                                                                    08:22:26
Unknown command: "rub"

To see a list of supported npm commands, run:
  npm help
~/code/node/node-demos ❯ npm run build                                                                    08:25:42

> node-demos@1.0.0 build
> npx tsc

~/code/node/node-demos ❯ node app/crypto/crypto.js                                                        08:25:47
checkPrimeSync:  true
generatePrimeSync:  ArrayBuffer {
  [Uint8Contents]: <0f 62 33 aa 3d 33 c8 b4 ec 60 08 c3 29>,
  byteLength: 13
}
getHashes:  [
  'RSA-MD5',
  'RSA-RIPEMD160',
  'RSA-SHA1',
  'RSA-SHA1-2',
  'RSA-SHA224',
  'RSA-SHA256',
  'RSA-SHA3-224',
  'RSA-SHA3-256',
  'RSA-SHA3-384',
  'RSA-SHA3-512',
  'RSA-SHA384',
  'RSA-SHA512',
  'RSA-SHA512/224',
  'RSA-SHA512/256',
  'RSA-SM3',
  'blake2b512',
  'blake2s256',
  'id-rsassa-pkcs1-v1_5-with-sha3-224',
  'id-rsassa-pkcs1-v1_5-with-sha3-256',
  'id-rsassa-pkcs1-v1_5-with-sha3-384',
  'id-rsassa-pkcs1-v1_5-with-sha3-512',
  'md5',
  'md5-sha1',
  'md5WithRSAEncryption',
  'ripemd',
  'ripemd160',
  'ripemd160WithRSA',
  'rmd160',
  'sha1',
  'sha1WithRSAEncryption',
  'sha224',
  'sha224WithRSAEncryption',
  'sha256',
  'sha256WithRSAEncryption',
  'sha3-224',
  'sha3-256',
  'sha3-384',
  'sha3-512',
  'sha384',
  'sha384WithRSAEncryption',
  'sha512',
  'sha512-224',
  'sha512-224WithRSAEncryption',
  'sha512-256',
  'sha512-256WithRSAEncryption',
  'sha512WithRSAEncryption',
  'shake128',
  'shake256',
  'sm3',
  'sm3WithRSAEncryption',
  'ssl3-md5',
  'ssl3-sha1'
]
getCiphers:  [
  'aes-128-cbc',
  'aes-128-cbc-hmac-sha1',
  'aes-128-cbc-hmac-sha256',
  'aes-128-ccm',
  'aes-128-cfb',
  'aes-128-cfb1',
  'aes-128-cfb8',
  'aes-128-ctr',
  'aes-128-ecb',
  'aes-128-gcm',
  'aes-128-ocb',
  'aes-128-ofb',
  'aes-128-xts',
  'aes-192-cbc',
  'aes-192-ccm',
  'aes-192-cfb',
  'aes-192-cfb1',
  'aes-192-cfb8',
  'aes-192-ctr',
  'aes-192-ecb',
  'aes-192-gcm',
  'aes-192-ocb',
  'aes-192-ofb',
  'aes-256-cbc',
  'aes-256-cbc-hmac-sha1',
  'aes-256-cbc-hmac-sha256',
  'aes-256-ccm',
  'aes-256-cfb',
  'aes-256-cfb1',
  'aes-256-cfb8',
  'aes-256-ctr',
  'aes-256-ecb',
  'aes-256-gcm',
  'aes-256-ocb',
  'aes-256-ofb',
  'aes-256-xts',
  'aes128',
  'aes128-wrap',
  'aes192',
  'aes192-wrap',
  'aes256',
  'aes256-wrap',
  'aria-128-cbc',
  'aria-128-ccm',
  'aria-128-cfb',
  'aria-128-cfb1',
  'aria-128-cfb8',
  'aria-128-ctr',
  'aria-128-ecb',
  'aria-128-gcm',
  'aria-128-ofb',
  'aria-192-cbc',
  'aria-192-ccm',
  'aria-192-cfb',
  'aria-192-cfb1',
  'aria-192-cfb8',
  'aria-192-ctr',
  'aria-192-ecb',
  'aria-192-gcm',
  'aria-192-ofb',
  'aria-256-cbc',
  'aria-256-ccm',
  'aria-256-cfb',
  'aria-256-cfb1',
  'aria-256-cfb8',
  'aria-256-ctr',
  'aria-256-ecb',
  'aria-256-gcm',
  'aria-256-ofb',
  'aria128',
  'aria192',
  'aria256',
  'camellia-128-cbc',
  'camellia-128-cfb',
  'camellia-128-cfb1',
  'camellia-128-cfb8',
  'camellia-128-ctr',
  'camellia-128-ecb',
  'camellia-128-ofb',
  'camellia-192-cbc',
  'camellia-192-cfb',
  'camellia-192-cfb1',
  'camellia-192-cfb8',
  'camellia-192-ctr',
  'camellia-192-ecb',
  'camellia-192-ofb',
  'camellia-256-cbc',
  'camellia-256-cfb',
  'camellia-256-cfb1',
  'camellia-256-cfb8',
  'camellia-256-ctr',
  'camellia-256-ecb',
  'camellia-256-ofb',
  'camellia128',
  'camellia192',
  'camellia256',
  'chacha20',
  'chacha20-poly1305',
  'des-ede',
  'des-ede-cbc',
  ... 31 more items
]
getCurves:  [
  'Oakley-EC2N-3',
  'Oakley-EC2N-4',
  'SM2',
  'brainpoolP160r1',
  'brainpoolP160t1',
  'brainpoolP192r1',
  'brainpoolP192t1',
  'brainpoolP224r1',
  'brainpoolP224t1',
  'brainpoolP256r1',
  'brainpoolP256t1',
  'brainpoolP320r1',
  'brainpoolP320t1',
  'brainpoolP384r1',
  'brainpoolP384t1',
  'brainpoolP512r1',
  'brainpoolP512t1',
  'c2pnb163v1',
  'c2pnb163v2',
  'c2pnb163v3',
  'c2pnb176v1',
  'c2pnb208w1',
  'c2pnb272w1',
  'c2pnb304w1',
  'c2pnb368w1',
  'c2tnb191v1',
  'c2tnb191v2',
  'c2tnb191v3',
  'c2tnb239v1',
  'c2tnb239v2',
  'c2tnb239v3',
  'c2tnb359v1',
  'c2tnb431r1',
  'prime192v1',
  'prime192v2',
  'prime192v3',
  'prime239v1',
  'prime239v2',
  'prime239v3',
  'prime256v1',
  'secp112r1',
  'secp112r2',
  'secp128r1',
  'secp128r2',
  'secp160k1',
  'secp160r1',
  'secp160r2',
  'secp192k1',
  'secp224k1',
  'secp224r1',
  'secp256k1',
  'secp384r1',
  'secp521r1',
  'sect113r1',
  'sect113r2',
  'sect131r1',
  'sect131r2',
  'sect163k1',
  'sect163r1',
  'sect163r2',
  'sect193r1',
  'sect193r2',
  'sect233k1',
  'sect233r1',
  'sect239k1',
  'sect283k1',
  'sect283r1',
  'sect409k1',
  'sect409r1',
  'sect571k1',
  'sect571r1',
  'wap-wsg-idm-ecid-wtls1',
  'wap-wsg-idm-ecid-wtls10',
  'wap-wsg-idm-ecid-wtls11',
  'wap-wsg-idm-ecid-wtls12',
  'wap-wsg-idm-ecid-wtls3',
  'wap-wsg-idm-ecid-wtls4',
  'wap-wsg-idm-ecid-wtls5',
  'wap-wsg-idm-ecid-wtls6',
  'wap-wsg-idm-ecid-wtls7',
  'wap-wsg-idm-ecid-wtls8',
  'wap-wsg-idm-ecid-wtls9'
]
getFips:  0
randomUUID:  a31e4ec7-3d02-4230-b450-3db2184a8d17
randomBytes:  <Buffer 8f 9c e6 3e 83 df 25 ce 93 4f 33 5b b8 49 1d 96>

```



**sign**



```ts
import * as C from 'node:crypto';

const {
    publicKey,
    privateKey,
} = C.generateKeyPairSync(
    'rsa', 
    {
        modulusLength: 4096,
        publicKeyEncoding: {
            type: 'spki',
            format: 'pem'
        },
        privateKeyEncoding: {
            type: 'pkcs8',
            format: 'pem',
        },
    }
);

console.log('publicKey: ', publicKey);
console.log('privateKey: ', privateKey);

const data = 'hello';
const sign = C.createSign('RSA-SHA256');
const signature = sign.update(data).sign(privateKey, 'hex');

console.log('signature: ', signature);

const verify = C.createVerify('RSA-SHA256');
verify.update(data);
const result = verify.verify(publicKey, signature, 'hex');
console.log('result: ', result);



```



**cipher**



```ts
import * as C from 'crypto';
/*
AES（Advanced Encryption Standard）支持多种加密模式，其中一些常见的包括：

ECB（Electronic Codebook）模式： 这是最简单的模式，将明文分成块，并对每个块单独加密。这种模式的问题在于相同的明文块会产生相同的密文块，因此可能存在安全性问题。

CBC（Cipher Block Chaining）模式： 在这个模式中，每个明文块在加密之前都会与前一个密文块进行异或运算，从而引入了块之间的依赖性。这有助于隐藏相同的明文块产生相同的密文块的问题。

CFB（Cipher Feedback）模式： 类似于CBC，但是在这种模式下，加密器的输出被反馈到输入中，而不是使用前一个密文块。这允许逐位加密，而不是逐块。

OFB（Output Feedback）模式： 与CFB类似，但是在这种模式下，加密器的输出用于生成密钥流，而不是反馈到输入中。密钥流然后与明文进行异或运算以产生密文。

CTR（Counter）模式： 在这个模式中，一个计数器被用作块的输入，而不是使用反馈。每个块都使用相同的密钥，但是计数器值不同，以确保生成不同的密文块。

*/
// const algorithm = 'aes-256-cbc';
// const key = C.randomBytes(32);
// const iv = C.randomBytes(16);
// function encrypt(data: string): string {
//     const encipher = C.createCipheriv(
//         algorithm,
//         Buffer.from(key),
//         iv
//     );
//     let ret = encipher.update(data, 'utf8', 'hex');
//     ret += encipher.final('hex');
//     return ret;
// }

// function decrypt(data: string): string {
//     const decipher = C.createDecipheriv(
//         algorithm,
//         Buffer.from(key),
//         iv
//     );
//     let ret = decipher.update(data, 'hex', 'utf8');
//     ret += decipher.final('utf8');
//     return ret;
// }

// const data = 'hellocvrtghrwtgcwe fersdgcergertverwff';

// const encryptData = encrypt(data);
// console.log('encryptData: ', encryptData);

// const decryptData = decrypt(encryptData);
// console.log('decryptData: ', decryptData);

 //////////////


const key = C.generateKeySync('aes', {
    length: 256
});
const iv = C.randomBytes(16);

const data = 'dbsyfgweuifn berhc fuihefhjhjkfvhufhjkvb';

console.log('key: ', JSON.stringify(key, undefined, '    '));

const encipher = C.createCipheriv('aes-256-cbc', key, iv);
const encryptData = encipher.update(data, 'utf8', 'hex') + encipher.final('hex');

console.log('encryptData: ', encryptData);
const decipher = C.createDecipheriv('aes-256-cbc', key, iv);
const decryptData = decipher.update(encryptData, 'hex', 'utf8') + decipher.final('utf8');

console.log('decryptData: ', decryptData);


```



**ecdh**



```ts
import * as C from 'crypto';

const a = C.createECDH('secp256k1');
const aKey = a.generateKeys();
console.log('akey: ', aKey.toString('hex'));


const b = C.createECDH('secp256k1');
const bKey = b.generateKeys();
console.log('bKey: ', bKey.toString('hex'));

const aPrivate = a.computeSecret(bKey);
const bPrivate = b.computeSecret(aKey);

console.log('aPrivate: ', aPrivate.toString('hex'));
console.log('bPrivate: ', bPrivate.toString('hex'));



```



**dh**



```ts
import * as C from 'crypto';

const a = C.createDiffieHellman(2048);
const aPublic = a.generateKeys();
console.log('aPublic: ', aPublic);

const b = C.createDiffieHellman(a.getPrime(), a.getGenerator());
const bPublic = b.generateKeys();
console.log('bPublic: ', bPublic);


const aPrivate = a.computeSecret(bPublic, null, 'hex');
const bPrivate = b.computeSecret(aPublic, null, 'hex');

console.log('aPrivate: ', aPrivate);
console.log('bPrivate: ', bPrivate);


```



**dhgroup**



```ts
import * as C from 'crypto';

// 创建 Diffie-Hellman Group 对象（使用组 14，2048 位）
const localDHGroup = C.createDiffieHellmanGroup('modp14');

// 生成本地密钥对
const localKeyPair = localDHGroup.generateKeys();


// 在对方处创建 Diffie-Hellman Group 对象
const remoteDHGroup = C.createDiffieHellmanGroup('modp14');

// 生成对方密钥对
const remoteKeyPair = remoteDHGroup.generateKeys();

// 将对方公钥传递给本地

// 本地计算共享密钥
const localSharedKey = localDHGroup.computeSecret(remoteKeyPair, null, 'hex');

// 对方计算共享密钥
const remoteSharedKey = remoteDHGroup.computeSecret(localKeyPair, null, 'hex');

console.log('Local Shared Key:', localSharedKey);
console.log('Remote Shared Key:', remoteSharedKey);

```



**hash**



```ts
import * as C from 'crypto';

// const md2 = C.createHash('md2');
// console.log('md2: ', md2.update('123456').digest('hex'));

// const md4 = C.createHash('md4');
// console.log('md4: ', md4.update('123456').digest('hex'));

const md5 = C.createHash('md5');
console.log('md5: ', md5.update('123456').digest('hex'));

const sha1 = C.createHash('sha1');
console.log('sha1: ', sha1.update('123456').digest('hex'));

const sha256 = C.createHash('sha256');
console.log('sha256: ', sha256.update('123456').digest('hex'));

const sha384 = C.createHash('sha384');
console.log('sha384: ', sha384.update('123456').digest('hex'));

const sha512 = C.createHash('sha512');
console.log('sha512: ', sha512.update('123456').digest('hex'));




```



**hmac**



```ts
import * as C from 'crypto';

const hmac = C.createHmac('sha256', 'key111');
console.log('hmac: ', hmac.update('msg123456').digest('hex'));
 
```



**x509**

```ts
import * as C from 'crypto';
import * as FS from 'fs';
console.log(process.cwd());
const data = FS.readFileSync(`./nodejs.cer`);
const cert = new C.X509Certificate(data);
console.log(JSON.stringify(cert, undefined, '    '));

```



# 29、Util



```ts
```





# 30、VM



```ts
```



# 31、V8



```ts
```



# 32、Debugger



```ts
const a = 1;
const b = 2;
const c = a + b;
console.log('hello');


// 
node inspect app/debugger/debugger.js
```



# 33、Inspector



```ts
```







# 34、Report



```sh
# node -h | grep report

  --report-compact            output compact single-line JSON
  --report-directory, --report-dir=...
                              define custom report pathname.
  --report-filename=...       define custom report file name.
  --report-on-fatalerror      generate diagnostic report on fatal
  --report-on-signal          generate diagnostic report upon
  --report-signal=...         causes diagnostic report to be produced
  --report-uncaught-exception generate diagnostic report on uncaught
  --test-reporter=...         report test output using the given
                              reporter
  --test-reporter-destination=...
                              report given reporter to the given
                              and ignore failure, reporting it to
```



```ts
# node --report-uncaught-exception app/report/report.js
function test(): void {
    const a = 1;
    const b = 2;
    throw new Error('hello');
    const c = a + b;
}

test();


# 

process.report!.reportOnUncaughtException = true;
function test(): void {
    const a = 1;
    const b = 2;
    throw new Error('hello');
    const c = a + b;
}

test();


```



# 35、





# 36、





# 37、



# 38、





# 39、







# 40、







# 41、





# 42、



























































































































































































