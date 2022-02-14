<script>
import '@fortawesome/fontawesome-free/js/all.js';
import CMap from './CMap.svelte';
import SMap from './SMap.svelte';
import LMap from './LMap.svelte';
</script>

<p>Redistricting occurs once every decade following the census count to equally redistribute  the state’s population between legislative and congressional districts. Districts that have seen population growth over the last 10 years will need to shrink geographically, while districts that shed population will need to get bigger in size. In Minnesota, the courts have taken over the process of drawing the state’s maps when divided government couldn’t reach agreement on their own. </p>

<!-- <p>Democrats currently hold control of the U.S. House and Senate and the redistricting process around the country is being closely watched ahead of this fall’s midterm elections. In 2020, Minnesota’s closest House race was in the second Congressional district that stretched from a district southern Twin Cities suburbs and the counties of Goodhue and Wabasha counties lining the Wisconsin border. Democratic Rep. Angie Craig won the district by about 2 percentage points and is already being targeted by the House GOP’s campaign arm as a possible pickup opportunity for the right this year.</p>

<p>The 2020 contest was also a close one for the southern Minnesota’s first Congressional seat held by Republican Rep. Jim Hagedorn. While Hagedorn won by around 3 percentage points, the seat could be in play for both Democrats and Republicans in future years.</p> -->

<p>The stakes are high for communities and voters navigating their new district lines, as well as for the state’s political parties, with redrawn boundaries scrambling election dynamics in districts for Congress and the state Legislature for the next decade. Every seat in the Legislature is up for re-election in a redistricting year, meaning control of state government is at stake in the fall election.</p>

<div class="rule"></div>
<h3 id="top">Jump to districts</h3>
<div class="jump"><a href="#congress">U.S. Congress</a> | <a href="#senate">Minnesota Senate</a> | <a href="#house">Minnesota House</a></div>

<h3>Notes</h3>
<div class="notes">
<ul>
<li>The maps below can display either the previous district borders or the newly redistricted boundaries with the click of a switch.</li>
<li>The basemap is Minnesota's 2020 presidential election precinct results between Joe Biden and Donald Trump. Precincts with closer results are shaded hues of yellow. All precincts have opacities based on relative vote density.</li>
<li>Searching for an address or location name should report which district its centered within. Note that some geographies like larger cities could be divided between multiple districts, so the results will simply report that municipality's centerpoint.</li>
</ul>
</div>

<div class="rule"></div>
<h2 class="chartTitle" id="congress">Congressional boundaries</h2>
<p>Minnesota’s eight seats in Congress are evenly split between the political parties, with four largely rural and suburban seats held by Republicans and four metro-area seats in Democrats’ hands. The state narrowly hung on to all eight of it’s seats in Congress after the latest population count, but population exploded in some suburban communities and declined in rural areas over the last decade, meaning district boundaries must still shift.</p>

<CMap/>

<div class="jump back"><a href="#top">Back to top</a></div>

<div class="rule"></div>
<h2 class="chartTitle" id="senate">State Senate boundaries</h2>
<p>Minnesota’s 67-seat state Senate is narrowly controlled by Republicans, who have dominated districts in rural areas of the state. Two state senators from the Iron Range have broken away from the Democrats and now caucus and often vote with Republicans. Democrats hold a majority of seats in the metro area, where most of the growth over the last decade took place. They’re hopeful for more seats in the new maps that lean Democratic. Political operatives on both sides are also watching for the fate of two Rochester-area state Senate districts that are currently controlled by Republicans.</p>

<SMap/>

<div class="jump back"><a href="#top">Back to top</a></div>

<div class="rule"></div>
<h2 class="chartTitle" id="house">State House boundaries</h2>
<p>Each state Senate district contains two House districts, which makes up the 134-seat Minnesota House of Representatives. Democrats have controlled the chamber since 2018, although their majority dramatically shrunk after the 2020 election. The party in power has swung back-and-forth over the years, and Republicans are hopeful they can take back control of the House with a favorable political climate heading into the 2022 midterm election. The most population growth, however, was in DFL-leaning suburbs and Twin Cities, meaning redrawn district boundaries could create new opportunities for the party.</p>

<LMap/>

<div class="jump back"><a href="#top">Back to top</a></div>