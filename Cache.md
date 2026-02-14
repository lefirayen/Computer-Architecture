<h1> What is Cache in Computer Architecture </h1>

<p>cache is a small but very fast memory, it is located near the CPU (Central Process Unit) that stores frequently used data from the main memory (RAM). in other words its man purpose is to reduce the average wait time it takes for the CPU to access data</p>
<br>
<p><strong>Note: </strong>without the cache the CPU would constantly wait for RAM (random access memory), which is much slower.</p>
<br>
<hr>
<br>
<h2>High Level Explaination</h2>
<ol>
  <li> CPU register: Instant access (smallest but fastest)</li>
  <li> Cache (L1, L2, L3): Very fast</li>
  <li> Ram: Slower</li>
  <li> SDD (Solid-State Drive) /HDD (hard disk drive): Much Slower</li>
</ol>
<br>
<br>
<h2>So Why Use Cache?</h2>
<p>modern CPUs operate in ~1 nanosecond per cycle. (not exact and differentiate). But in the other hand RAM takes 50-100+ nanoseconds to get accessed (i.e the CPU could sit idle for 100 or more cycles waiting for data)
this is where the cache comes in.</p>
<br>
<hr>
<br>
<h2>But Why Are There 3 Levels of Cache?</h2>
<p>Modern Processors (e.g Intel and AMD CPUs) Use L1, L2, L3 cache levels to balance out:</p>
<ul>
  <li>Speed</li>
  <li>Size</li>
  <li>Cost</li>
  <li>Power Consumption</li>
</ul>

<h2>Cache Hierachy:</h2>
<h3>L1 Cache</h3>
<ul>
  <li>Smallest (32KB - 128KB per core)</li>
  <li>Fastest (1 - 4 cycles)</li>
  <li>Located inside each CPU core</li>
  <li>Split Into:<br>
    <ul>
      <li>L1 Instruction Cache</li>
      <li>L1 Data Cache</li>
    </ul>
  </li>
</ul>
<h3>L2 Cache</h3>
<ul>
  <li>Larger (256KB - 1MB per core)</li>
  <li>Slightly slower (5 - 15 cycles)</li>
  <li>Still per core</li>
</ul>
<h3>L3 Cache</h3>
<ul>
  <li>Much Larger (Several MBs)</li>
  <li>Shared among cores</li>
  <li>Slower (20 - 50 cylces)</li>
  <li>Acts as last on-chip buffer before RAM</li>
</ul>
<h2>Why not just make L1 huge?</h2>
<p>We don't make the L1 cache arbitrarily large because increasing its capacity increases physical wire lengths and routing complexity, which raises access latency due to singal propagation delay and higher capacitance, which also requiring many more transistors that increase area, dynamic and leakage power, and overall cost; instead, modern processors use a hierarchial cache desih, in which small, low latency L1 cachese are backed by progressively larger and slower L2 and L3 caches to balance hit rate access time, energy efficiency, and silicon area</p>
<br>
<hr>
<br>

<h2>How Cache Works (Low-Level Explanation)</h2>
<h3>Memory is Divided into Cache Lines</h3>
<p>Typical cache line size: 64 bytes</p>
<p>For example if you request:</p>
<pre>
  <code class="c">
    int x = arr[0];
  </code>
</pre>
<p>The CPU does not just load <code>arr[0]</code>.</p>
<p>it loads:</p>
<pre>
  <code>
    arr[0] to arr[15] (assuming 4-byte ints and 64-byte line)
  </code>
</pre>
<p>This is called <strong>Spatial Locatlity</strong>. if you access one memory location, you will likely acess nearby ones</p>
<br>
<h3>Cache Hit vs Cache Miss</h3>
<p>When CPU needs data:</p>
<ol>
  <li>Check L1</li>
  <li>if miss then check L2</li>
  <li>if miss then check L3</li>
  <li>if miss then fetch from RAM</li>
</ol>

<h3>Cache Hit</h3>
<p>Data found in cache -> fast.</p>
<h3>Cache Miss</h3>
<p>Data not found -> fetch from lower level memory (slow)</p>
<br>
<h3>Example 1: Cache-Friendly Code</h3>
<pre>
  <code class="c">
    int sum = 0;
    for (int i = 0 ; i < 1000000 ; i++) {
      sum += arr[i];
    }
  </code>
</pre>
<h4>Why is fast?</h4>
<p>this loop accesses memory sequentially one by one, Each cache line is fetched fully used so this mean we have a high cache hit rate and very low cache miss rate</p>
<br>
<h3>Example 2:</h3>
<pre>
  <code class="c">
    int sum = 0;
    for (int i = 0 ; i < 1000000 ; i++) {
      sum += arr[rand() % 1000000];
    }
  </code>
</pre>
<h4>Why is this slow?</h4>
<p>You could probably make a very good guess towards why this is a very slow way to access memory, the reason is why are jumping in random memory everytime, that means out cache are rarely used this causes high cache miss rates and frequent RAM access beacuse all the cache levels failed to get the data needed</p>
<br>
<hr>
<br>
<h3>Low-Level Hard View</h3>
<p>Each Cache line stores couple of things (Tag, Valid Bit, Dirty Bit, Data (64 bytes)), first what is a tag? when memory address is requested for example a 64-bit address we got Tag, index and offset, the offset is which byte in cache line the index is which cache set and the is the identifier for which memory block. So the CPU compares tag to see if the data matches or not</p>

<br>
<hr>
<br>

<h2>Cache Mapping Types</h2>
<h3>Direct-Mapped</h3>
<p>Each memory block maps to exactly one cache location.</p>
<p>Fast but more conflicts could occur</p>
<h3>Set-Associative (Most Common)</h3>
<p>Memory can map to multiple possible locations (e.g, 8-way).</p>
<p>Better balance of speed and flexibility</p>
<h3>Fully Associative</h3>
<p>Memory block can go anywhere.</p>
<p>Very expensive hardware.</p>
<br>
<h2>Cache Coherency (Multi-Core Systems)</h2>
<p>In a multi-core CPU:</p>
<p>Each core has its own L1 & L2 cache</p>
<strong>Problem:</strong><br>
<p>If Core 1 updates variable X then</p>
<p>Core 2 must see the update.</p>

<strong>Solution:</strong><br>
<p>Cache coherency Protocols</p>

<h3>Example:</h3>
<h4>MESI protocol</h4>
<ul>
  <li>Modified</li>
  <li>Exclusive</li>
  <li>Shared</li>
  <li>Invalid</li>
</ul>
<p>This ensures memory consistency</p>
<br>
<hr>
<br>
<p>      CPU Request              </p>
<p>           ↓                   </p>     
<p>Check For L1 (1 - 4 cycles)    </p>
<p>           ↓ miss             </p>
<p>Check For L2 (5 - 15 cycles)  </p>
<p>           ↓ miss?            </p>
<p>Check For L3 (20 - 50 cycles) </p>
<p>           ↓ miss?           </p>
<p>Fetch from RAM (100+ cycles) </p>
<br><hr><br>

<h2>Summary & Notes</h2>
<h3>Why Cache Improves Performance So Much?</h3>
<p>Programs exhibit:</p>
<ol>
  <li>Temporal Locatily (Recently used data likely reused)</li>
  <li>Spatial Locatlity (Nearby data likely used soon)</li>
</ol>
<p>Cache exploits both.</p>

<h3>Performance of a Cache</h3>
<p>Supposing:</p>
<ul>
  <li>L1 hit : 4 cycles</li>
  <li>L2 hit : 12 cycles</li>
  <li>L3 hit : 40 cycles</li>
  <li>RAM : 120 cycles</li>
</ul>
<br>
<h3>Average Memory Access Time (AMAT):</h3>
<code> AMAT = Hit Time + Miss Rate × Miss Penalty</code>

<p>This drastically reduces average latency.</p>





