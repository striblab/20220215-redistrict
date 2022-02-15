<script>
import '@fortawesome/fontawesome-free/js/all.js';
import CMap from './CMap.svelte';
import SMap from './SMap.svelte';
import LMap from './LMap.svelte';
</script>

<p>Once every decade, the political boundaries of the state shift and help determine elections for the next 10 years.</p>

<p>It’s called redistricting, and occurs <a href="
https://www.startribune.com/minnesota-won-t-lose-eighth-congressional-seat-census-bureau-rules/600050299/">after the census count</a> to equally redistribute the state’s population between legislative and congressional districts. Districts that have seen population growth over the last decade need to shrink, while districts that shed population need to get bigger in size. In Minnesota, the <a href="https://www.startribune.com/courts-hear-arguments-in-redistricting-case-ahead-of-anticipated-release-of-new-state-maps/600132504/
Change">courts have taken over the process this year</a> — as they have for the last five decades — because the divided Legislature couldn’t strike an agreement on its own.</p>

<p>The stakes are high for communities and voters navigating their new district lines, as well as for the state’s political parties, with redrawn boundaries scrambling election dynamics in districts for Congress and the state Legislature. Every legislative seat is up for re-election in a redistricting year, meaning control of state government is at stake in the fall election. </p>

<div class="rule"></div>
<h3 id="top">Jump to districts</h3>
<div class="jump"><a href="#congress">U.S. Congress</a> | <a href="#senate">Minnesota Senate</a> | <a href="#house">Minnesota House</a></div>


<div class="notes">
<p>The maps below can display either the previous district borders or the newly redistricted boundaries with the click of a switch. The basemap is Minnesota's 2020 presidential election precinct results between Joe Biden and Donald Trump. Precincts with closer results are shaded hues of yellow. All precincts have opacities based on relative vote density. Searching for an address or location name should report which district its centered within. Note that some geographies like larger cities could be divided between multiple districts, so the results will simply report that municipality's centerpoint.</p>
</div>

<div class="rule"></div>
<h2 class="chartTitle" id="congress">Congressional boundaries</h2>
<p>Minnesota’s eight seats in Congress are evenly split between the political parties, with four largely rural and exurban seats held by Republicans and four urban and largely suburban seats in Democrats’ hands. The state barely <a href="https://www.startribune.com/minnesota-won-t-lose-eighth-congressional-seat-census-bureau-rules/600050299/">hung on to all eight Congressional seats</a> after the latest population count, but population exploded in suburban communities and declined in rural areas over the last decade, meaning some districts look a lot different under the new maps. </p>

<p>The shape of the second district saw significant changes, adding Le Sueur County and cutting out Wabasha and Goodhue counties — which were added to the first Congressional district that extends along the Iowa border.</p>

<CMap/>

<div class="jump back"><a href="#top">Back to top</a></div>

<p>With Democrats narrowly in control of the U.S. House and Senate, the redistricting process around the country is being closely watched ahead of this fall’s midterm elections. In 2020, <a href="https://www.startribune.com/minnesota-u-s-rep-angie-craig-wins-in-second-district-over-gop-challenger-tyler-kistner/572969752/">Minnesota’s closest House race</a> was in the second Congressional district that included southern Twin Cities suburbs and the counties of Wabasha and Goodhue. Democratic Rep. Angie Craig won the district by about 2 percentage points and is <a href="https://www.startribune.com/u-s-rep-angie-craig-faces-midterm-headwinds/600138573/">already being targeted by the House GOP’s campaign arm</a> as a possible pickup opportunity for the right this year.</p>

<p>The 2020 contest was also a close one for southern Minnesota’s <a href="https://www.startribune.com/hagedorn-declares-victory-in-minnesota-s-first-congressional-district/572970632/">first Congressional seat</a> held by Republican Rep. Jim Hagedorn. While Hagedorn won by around 3 percentage points, the seat could be in play for both Democrats and Republicans in future years. </p>

<p>The sprawling eighth district, represented by GOP Rep. Pete Stauber, expanded westward in northern Minnesota. The district also added part of Washington County in its southernmost corner near the metro.</p>

<div class="rule"></div>
<h2 class="chartTitle" id="senate">State Senate boundaries</h2>
<p>Minnesota’s 67-seat state Senate is narrowly controlled by Republicans, who have dominated in most districts in rural areas of the state. Two state senators from the Iron Range have broken away from the Democrats and now caucus and often vote with Republicans. </p>

<p>Democrats hold a majority of seats in the metro area, where most of the growth over the last decade took place. They’re hopeful for more seats in the new maps that lean Democratic.</p>

<SMap/>

<div class="jump back"><a href="#top">Back to top</a></div>

<div class="rule"></div>
<h2 class="chartTitle" id="house">State House boundaries</h2>
<p>Each state Senate district contains two House districts, which make up the 134-seat Minnesota House of Representatives. Democrats have controlled the chamber since 2018, although their majority shrunk dramatically after the 2020 election. The current makeup is 69 DFLers, 63 GOPs and two members who don’t caucus with either party.</p>

<LMap/>

<div class="jump back"><a href="#top">Back to top</a></div>

<p>The party in power has swung back-and-forth over the years, and Republicans are hopeful they can take back control of the House with a favorable political climate heading into the 2022 midterm election. The most population growth, however, was in DFL-leaning suburbs and Twin Cities, meaning redrawn district boundaries could create new opportunities for the party.</p>