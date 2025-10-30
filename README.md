# ICT-smart-money-with-alerts
ICT smart money with alerts
//@version=5
indicator("ICT smartmoney with alerts", max_lines_count=500, max_boxes_count=500, max_labels_count=500, max_bars_back=3000, overlay=true)

//-----------------------------------------------------------------------------}
//Strings
//-----------------------------------------------------------------------------{
o          = "Options"
sp1        = "       "
sp2        = "              "
hl         = "High / Low    " + sp1
ny_        = "New York"       + sp1
lo_        = "London Open"
lc_        = "London Close"
as_        = "Asian"

//-----------------------------------------------------------------------------}
//Settings
//-----------------------------------------------------------------------------{
i_mode     = input.string("Present", "Mode", options = ["Present","Historical"])

//Market Structure Shift
showMS     = input.bool  (true , "", group = "Market Structures", inline = "MS")
len        = input.int   (5    , "      Length   " + sp2, group = "Market Structures", inline = "MS", minval = 3, maxval = 10)

iMSS       = input.bool  (true , "       MSS" + sp1, group = "Market Structures", inline = "M1")
cMSSbl     = input.color (color.new(#00e6a1, 0), "bullish", group = "Market Structures", inline = "M1")
cMSSbr     = input.color (color.new(#e60400, 0), "bearish", group = "Market Structures", inline = "M1")

iBOS       = input.bool  (true , "       BOS" + sp1, group = "Market Structures", inline = "BS")
cBOSbl     = input.color (color.new(#00e6a1, 0), "bullish", group = "Market Structures", inline = "BS")
cBOSbr     = input.color (color.new(#e60400, 0), "bearish", group = "Market Structures", inline = "BS")

//Displacement
sDispl     = input.bool  (false, "Show Displacement", group = "Displacement")

perc_Body  = 0.36
bxBack     = 10

sVimbl     = input.bool  (true , "", group = "Volume Imbalance", inline = "VI")
visVim     = input.int   (2    , "   # Visible VI's  " + sp1, group = "Volume Imbalance", inline = "VI", minval = 2, maxval = 100)
cVimbl     = input.color (color.new(#06b2d0, 0), "", group = "Volume Imbalance", inline = "VI")

//Order Blocks
showOB     = input.bool  (true , "Show Order Blocks", group = "Order Blocks")
length     = input.int   (10   , "Swing Lookback"   , group = "Order Blocks", minval = 3)
showBull   = input.int   (1    , "Show Last Bullish OB", group = "Order Blocks", minval = 0)
showBear   = input.int   (1    , "Show Last Bearish OB", group = "Order Blocks", minval = 0)
useBody    = input.bool  (true , "Use Candle Body")

//OB Style
bullCss    = input.color (color.new(#3e89fa, 0) , "Bullish OB  ", group = "Order Blocks", inline = "bullcss")
bullBrkCss = input.color (color.new(#4785f9, 85), "Bullish Break  ", group = "Order Blocks", inline = "bullcss")

bearCss    = input.color (color.new(#FF3131, 0) , "Bearish OB", group = "Order Blocks", inline = "bearcss")
bearBrkCss = input.color (color.new(#f9ff57, 85), "Bearish Break", group = "Order Blocks", inline = "bearcss")

showLabels = input.bool  (false, "Show Historical Polarity Changes", group = "Order Blocks")

//Liquidity
showLq     = input.bool  (true , "Show Liquidity", group = "Liquidity")
a          = 10 / input.float(4, "margin", group = "Liquidity", step = 0.1, minval = 2, maxval = 7)
visLiq     = input.int   (2, "# Visible Liq. boxes", group = "Liquidity", minval = 1, maxval = 50, tooltip = "In the same direction")
cLIQ_B     = input.color (color.new(#fa451c, 0), "Buyside Liquidity  ", group = "Liquidity")
cLIQ_S     = input.color (color.new(#1ce4fa, 0), "Sellside Liquidity", group = "Liquidity")

//FVG
shwFVG     = input.bool  (true , "Show FVGs", group = "Fair Value Gaps")
i_BPR      = input.bool  (false, "Balance Price Range", group = "Fair Value Gaps")
i_FVG      = input.string("FVG", o, group = "Fair Value Gaps", options = ["FVG","IFVG"], tooltip = "Fair Value Gaps\nor\nImplied Fair Value Gaps")
visBxs     = input.int   (2    , "# Visible FVG's", group = "Fair Value Gaps", minval = 1, maxval = 20, tooltip = "In the same direction")

//FVG Style
cFVGbl     = input.color (color.new(#00e676, 0), "Bullish FVG  ", group = "Fair Value Gaps", inline = "FVGbl")
cFVGblBR   = input.color (color.new(#808000, 0), "Break"       , group = "Fair Value Gaps", inline = "FVGbl")
cFVGbr     = input.color (color.new(#ff5252, 0), "Bearish FVG ", group = "Fair Value Gaps", inline = "FVGbr")
cFVGbrBR   = input.color (color.new(#FF0000, 0), "Break"       , group = "Fair Value Gaps", inline = "FVGbr")

//NWOG/NDOG
iNWOG      = input.bool  (true , "" , group = "NWOG/NDOG", inline = "NWOG")
cNWOG1     = input.color (color.new(#ff5252, 28), "NWOG    ", group = "NWOG/NDOG", inline = "NWOG")
cNWOG2     = input.color (color.new(#b2b5be, 50), ""       , group = "NWOG/NDOG", inline = "NWOG")
maxNWOG    = input.int   (3    , "Show max", group = "NWOG/NDOG", inline = "NWOG", minval = 0, maxval = 50)

iNDOG      = input.bool  (false, "" , group = "NWOG/NDOG", inline = "NDOG")
cNDOG1     = input.color (color.new(#ff9800, 20), "NDOG    ", group = "NWOG/NDOG", inline = "NDOG")
cNDOG2     = input.color (color.new(#4dd0e1, 65), ""       , group = "NWOG/NDOG", inline = "NDOG")
maxNDOG    = input.int   (1    , "Show max", group = "NWOG/NDOG", inline = "NDOG", minval = 0, maxval = 50)

//Fibonacci
iFib       = input.string("NONE", "Fibonacci between last: ", group = "Fibonacci", options = ["FVG","BPR","OB","Liq","VI","NWOG","NONE"])
iExt       = input.bool  (false , "Extend lines", group = "Fibonacci")

//Killzones
showKZ     = input.bool  (false, "Show Killzones", group = "Killzones")
showNy     = input.bool  (true , ny_           , group = "Killzones", inline = "ny")
nyCss      = input.color (color.new(#ff5d00, 93), "" , group = "Killzones", inline = "ny")
showLdno   = input.bool  (true , lo_           , group = "Killzones", inline = "lo")
ldnoCss    = input.color (color.new(#00bcd4, 93), "" , group = "Killzones", inline = "lo")
showLdnc   = input.bool  (true , lc_           , group = "Killzones", inline = "lc")
ldncCss    = input.color (color.new(#2157f3, 93), "" , group = "Killzones", inline = "lc")
showAsia   = input.bool  (true , as_ + sp2     , group = "Killzones", inline = "as")
asiaCss    = input.color (color.new(#e91e63, 93), "" , group = "Killzones", inline = "as")

// окремі session-інпути
sessNY     = input.session("0700-0900", "", group = "Killzones", inline = "ny")
sessLO     = input.session("0700-1000", "", group = "Killzones", inline = "lo")
sessLC     = input.session("1500-1700", "", group = "Killzones", inline = "lc")
sessAS     = input.session("1000-1400", "", group = "Killzones", inline = "as")

//-----------------------------------------------------------------------------}
//General Calculations
//-----------------------------------------------------------------------------{
n          = bar_index
hi         = high
lo         = low
tf_msec    = timeframe.in_seconds(timeframe.period) * 1000
maxSize    = 50
atr        = ta.atr(10)
per        = i_mode == "Present" ? last_bar_index - bar_index <= 500 : true
perB       = last_bar_index - bar_index <= 1000
xloc       = iFib == "OB" ? xloc.bar_time : xloc.bar_index
ext        = iExt ? extend.right : extend.none
plus       = iFib == "OB" ? tf_msec * 50 : 50
mx         = math.max(close, open)
mn         = math.min(close, open)
body       = math.abs(close - open)
meanBody   = ta.sma(body, len)
max        = useBody ? mx : high
min        = useBody ? mn : low
blBrkConf  = 0
brBrkConf  = 0
r          = color.r(chart.bg_color)
g          = color.g(chart.bg_color)
b          = color.b(chart.bg_color)
isDark     = r < 80 and g < 80 and b < 80

//-----------------------------------------------------------------------------}
//User Defined Types
//-----------------------------------------------------------------------------{
type ZZ
    int[]   d
    int[]   x
    float[] y
    bool[]  b

type ln_d
    line  l
    int   d

type _2ln_lb
    line  l1
    line  l2
    label lb

type bx_ln
    box   b
    line  l

type bx_ln_lb
    box   bx
    line  ln
    label lb

type mss
    int     dir
    line[]  l_mssBl
    line[]  l_mssBr
    line[]  l_bosBl
    line[]  l_bosBr
    label[] lbMssBl
    label[] lbMssBr
    label[] lbBosBl
    label[] lbBosBr

type liq
    box   bx
    bool  broken
    bool  brokenTop
    bool  brokenBtm
    line  ln

type ob
    float top       = na
    float btm       = na
    int   loc       = bar_index
    bool  breaker   = false
    int   break_loc = na

type swing
    float y = na
    int   x = na
    bool  crossed = false

type FVG
    box   box
    bool  active
    int   pos

// ініціалізації в один рядок
var mss MSS = mss.new(0, array.new_line(0), array.new_line(0), array.new_line(0), array.new_line(0), array.new_label(0), array.new_label(0), array.new_label(0), array.new_label(0))

//-----------------------------------------------------------------------------}
//Variables
//-----------------------------------------------------------------------------{
maxVimb = 2

var float friCp = na, var int friCi = na
var float monOp = na, var int monOi = na

var float prDCp = na, var int prDCi = na
var float cuDOp = na, var int cuDOi = na

var _2ln_lb[] Vimbal  = array.new<_2ln_lb>(0)

var liq[] b_liq_B    = array.new<liq>(1, liq.new(box(na), false, false, false, line(na)))
var liq[] b_liq_S    = array.new<liq>(1, liq.new(box(na), false, false, false, line(na)))

var ob[]  bullish_ob = array.new<ob>(0)
var ob[]  bearish_ob = array.new<ob>(0)

var bx_ln[]    bl_NWOG    = array.new<bx_ln>(0)
var bx_ln[]    bl_NDOG    = array.new<bx_ln>(0)

var bx_ln_lb[] a_bx_ln_lb = array.new<bx_ln_lb>(0)

var FVG[] bFVG_UP  = array.new<FVG>(0)
var FVG[] bFVG_DN  = array.new<FVG>(0)
var FVG[] bBPR_UP  = array.new<FVG>(0)
var FVG[] bBPR_DN  = array.new<FVG>(0)

var ZZ aZZ = ZZ.new(array.new_int(maxSize, 0), array.new_int(maxSize, 0), array.new_float(maxSize, na), array.new_bool(maxSize, na))

var line _diag = line.new(na, na, na, na, color=color.new(color.silver, 50), style=line.style_dashed, xloc=xloc)
var line _vert = line.new(na, na, na, na, color=color.new(color.silver, 50), style=line.style_dotted, xloc=xloc)
var line _zero = line.new(na, na, na, na, color=color.new(color.silver,  5), style=line.style_solid , xloc=xloc, extend=ext)
var line _0236 = line.new(na, na, na, na, color=color.new(color.orange, 25), style=line.style_solid , xloc=xloc, extend=ext)
var line _0382 = line.new(na, na, na, na, color=color.new(color.yellow, 25), style=line.style_solid , xloc=xloc, extend=ext)
var line _0500 = line.new(na, na, na, na, color=color.new(color.green , 25), style=line.style_solid , xloc=xloc, extend=ext)
var line _0618 = line.new(na, na, na, na, color=color.new(color.yellow, 25), style=line.style_solid , xloc=xloc, extend=ext)
var line _0786 = line.new(na, na, na, na, color=color.new(color.orange, 25), style=line.style_solid , xloc=xloc, extend=ext)
var line _one_ = line.new(na, na, na, na, color=color.new(color.silver,  5), style=line.style_solid , xloc=xloc, extend=ext)
var line _1618 = line.new(na, na, na, na, color=color.new(color.yellow, 25), style=line.style_solid , xloc=xloc, extend=ext)

//-----------------------------------------------------------------------------}
//Functions/methods
//-----------------------------------------------------------------------------{
method in_out(ZZ aZZ, int d, int x1, float y1, int x2, float y2, color col, bool b) =>
    aZZ.d.unshift(d), aZZ.x.unshift(x2), aZZ.y.unshift(y2), aZZ.b.unshift(b), aZZ.d.pop(), aZZ.x.pop(), aZZ.y.pop(), aZZ.b.pop()

method timeinrange(string res, string sess) => not na(time(timeframe.period, res, sess))

method setLine(line ln, int x1, float y1, int x2, float y2) => ln.set_xy1(x1, y1), ln.set_xy2(x2, y2)

method clear_aLine(line[] l) =>
    if l.size() > 0
        for i = l.size() - 1 to 0
            l.pop().delete()

method clear_aLabLin(label[] l) =>
    if l.size() > 0
        for i = l.size() - 1 to 0
            l.pop().delete()

method notransp(color css) => color.rgb(color.r(css), color.g(css), color.b(css))

method display(ob id, css, break_css, str) =>
    if showOB
        if id.breaker
            _b = box.new(id.loc, id.top, timenow + (tf_msec * 10), id.btm, xloc = xloc.bar_time, bgcolor = break_css, extend = extend.none)
            a_bx_ln_lb.unshift(bx_ln_lb.new(_b, line(na), label(na)))
        else
            y = str == "bl" ? id.btm : id.top
            s = str == "bl" ? label.style_label_up : label.style_label_down
            a_bx_ln_lb.unshift(bx_ln_lb.new(box(na), line.new(id.loc, y, id.loc + (tf_msec * 10), y, xloc = xloc.bar_time, color = css, width = 2), label.new(id.loc + (tf_msec * 10), y, text = str == "bl" ? "+OB" : "-OB", xloc = xloc.bar_time, style = s, color = color(na), textcolor = css, size = size.small)))

swings(len) =>
    var os = 0
    var swing top = swing.new(na, na)
    var swing btm = swing.new(na, na)
    upper = ta.highest(len)
    lower = ta.lowest(len)
    os := high[len] > upper ? 0 : low[len] < lower ? 1 : os
    if os == 0 and os[1] != 0
        top := swing.new(high[length], bar_index[length])
    if os == 1 and os[1] != 1
        btm := swing.new(low[length], bar_index[length])
    [top, btm]

set_lab(i, str) =>
    style = str == "Bl" ? label.style_label_down : label.style_label_up
    txcol = str == "Bl" ? color.lime : color.red
    label.new(math.round(math.avg(aZZ.x.get(i), n)), aZZ.y.get(i), text = "BOS", style = style, color = color(na), textcolor = txcol, size = size.tiny)

set_lin(i, str) =>
    colr = str == "Bl" ? color.lime : color.red
    line.new(aZZ.x.get(i), aZZ.y.get(i), n, aZZ.y.get(i), color = colr, style = line.style_dotted)

// Return MSS triggers from draw(): [bull, bear]
draw(left, col) =>
    bool bullMSSLocal = false
    bool bearMSSLocal = false

    max_bars_back(time, 1000)
    var int dir = na, var int x1 = na, var float y1 = na, var int x2 = na, var float y2 = na

    sz = aZZ.d.size()
    x2 := bar_index - 1
    ph = ta.pivothigh(hi, left, 1)
    pl = ta.pivotlow (lo, left, 1)

    if ph
        dir := aZZ.d.get(0), x1 := aZZ.x.get(0), y1 := aZZ.y.get(0), y2 := nz(hi[1])
        if dir < 1
            aZZ.in_out(1, x1, y1, x2, y2, col, true)
        else
            if dir == 1 and ph > y1
                aZZ.x.set(0, x2), aZZ.y.set(0, y2)
        if showLq and per and sz > 0
            count = 0, st_P = 0.0, st_B = 0, minP = 0.0, maxP = 10e6
            for i = 0 to math.min(sz, 50) - 1
                if aZZ.d.get(i) == 1
                    if aZZ.y.get(i) > ph + (atr / a)
                        break
                    else
                        if aZZ.y.get(i) > ph - (atr / a) and aZZ.y.get(i) < ph + (atr / a)
                            count += 1, st_B := aZZ.x.get(i), st_P := aZZ.y.get(i)
                            if aZZ.y.get(i) > minP
                                minP := aZZ.y.get(i)
                            if aZZ.y.get(i) < maxP
                                maxP := aZZ.y.get(i)
            if count > 2
                getB = b_liq_B.get(0)
                if st_B == getB.bx.get_left()
                    getB.bx.set_top(math.avg(minP, maxP) + (atr / a)), getB.bx.set_rightbottom(n + 10, math.avg(minP, maxP) - (atr / a))
                else
                    b_liq_B.unshift(liq.new(box.new(st_B, math.avg(minP, maxP) + (atr / a), n + 10, math.avg(minP, maxP) - (atr / a), bgcolor = color(na), border_color = color(na), text = "Buyside liquidity", text_size = size.tiny, text_halign = text.align_left, text_valign = text.align_bottom, text_color = color.new(cLIQ_B, 25)), false, false, false, line.new(st_B, st_P, n - 1, st_P, color = color.new(cLIQ_B, 0))))
                if b_liq_B.size() > visLiq
                    getLast = b_liq_B.pop(), getLast.bx.delete(), getLast.ln.delete()

    if pl
        dir := aZZ.d.get(0), x1 := aZZ.x.get(0), y1 := aZZ.y.get(0), y2 := nz(lo[1])
        if dir > -1
            aZZ.in_out(-1, x1, y1, x2, y2, col, true)
        else
            if dir == -1 and pl < y1
                aZZ.x.set(0, x2), aZZ.y.set(0, y2)
        if showLq and per and sz > 0
            count = 0, st_P = 0.0, st_B = 0, minP = 0.0, maxP = 10e6
            for i = 0 to math.min(sz, 50) - 1
                if aZZ.d.get(i) == -1
                    if aZZ.y.get(i) < pl - (atr / a)
                        break
                    else
                        if aZZ.y.get(i) > pl - (atr / a) and aZZ.y.get(i) < pl + (atr / a)
                            count += 1, st_B := aZZ.x.get(i), st_P := aZZ.y.get(i)
                            if aZZ.y.get(i) > minP
                                minP := aZZ.y.get(i)
                            if aZZ.y.get(i) < maxP
                                maxP := aZZ.y.get(i)
            if count > 2
                getB = b_liq_S.get(0)
                if st_B == getB.bx.get_left()
                    getB.bx.set_top(math.avg(minP, maxP) + (atr / a)), getB.bx.set_rightbottom(n + 10, math.avg(minP, maxP) - (atr / a))
                else
                    b_liq_S.unshift(liq.new(box.new(st_B, math.avg(minP, maxP) + (atr / a), n + 10, math.avg(minP, maxP) - (atr / a), bgcolor = color(na), border_color = color(na), text = "Sellside liquidity", text_size = size.tiny, text_halign = text.align_left, text_valign = text.align_bottom, text_color = color.new(cLIQ_S, 25)), false, false, false, line.new(st_B, st_P, n - 1, st_P, color = color.new(cLIQ_S, 0))))
                if b_liq_S.size() > visLiq
                    getLast = b_liq_S.pop(), getLast.bx.delete(), getLast.ln.delete()

    // --- Market Structure Shift (MSS) ---
    if showMS
        iH = aZZ.d.get(2) ==  1 ? 2 : 1
        iL = aZZ.d.get(2) == -1 ? 2 : 1
        switch
            close > aZZ.y.get(iH) and aZZ.d.get(iH) == 1 and MSS.dir < 1 =>
                MSS.dir := 1
                if i_mode == "Present"
                    MSS.l_bosBl.clear_aLine(), MSS.l_bosBr.clear_aLine(), MSS.lbBosBl.clear_aLabLin(), MSS.lbBosBr.clear_aLabLin(), MSS.l_mssBl.clear_aLine(), MSS.l_mssBr.clear_aLine(), MSS.lbMssBl.clear_aLabLin(), MSS.lbMssBr.clear_aLabLin()
                MSS.l_mssBl.unshift(line.new(aZZ.x.get(iH), aZZ.y.get(iH), n, aZZ.y.get(iH), color = cMSSbl))
                MSS.lbMssBl.unshift(label.new(math.round(math.avg(aZZ.x.get(iH), n)), aZZ.y.get(iH), text = "MSS", style = label.style_label_down, size = size.tiny, color = color(na), textcolor = cMSSbl))
                bullMSSLocal := true
            close < aZZ.y.get(iL) and aZZ.d.get(iL) == -1 and MSS.dir > -1 =>
                MSS.dir := -1
                if i_mode == "Present"
                    MSS.l_bosBl.clear_aLine(), MSS.l_bosBr.clear_aLine(), MSS.lbBosBl.clear_aLabLin(), MSS.lbBosBr.clear_aLabLin(), MSS.l_mssBl.clear_aLine(), MSS.l_mssBr.clear_aLine(), MSS.lbMssBl.clear_aLabLin(), MSS.lbMssBr.clear_aLabLin()
                MSS.l_mssBr.unshift(line.new(aZZ.x.get(iL), aZZ.y.get(iL), n, aZZ.y.get(iL), color = cMSSbr))
                MSS.lbMssBr.unshift(label.new(math.round(math.avg(aZZ.x.get(iL), n)), aZZ.y.get(iL), text = "MSS", style = label.style_label_up, size = size.tiny, color = color(na), textcolor = cMSSbr))
                bearMSSLocal := true
        if not iMSS
            MSS.l_mssBl.get(0).set_color(color(na)), MSS.lbMssBl.get(0).set_textcolor(color(na)), MSS.l_mssBr.get(0).set_color(color(na)), MSS.lbMssBr.get(0).set_textcolor(color(na))
    [bullMSSLocal, bearMSSLocal]

//-----------------------------------------------------------------------------}
//Calculations
//-----------------------------------------------------------------------------{
[bullMSSTrig, bearMSSTrig] = draw(len, color.yellow)  // багатозначне присвоєння через '='

if MSS.l_bosBl.size() > 200
    MSS.l_bosBl.pop().delete(), MSS.lbBosBl.pop().delete()
if MSS.l_bosBr.size() > 200
    MSS.l_bosBr.pop().delete(), MSS.lbBosBr.pop().delete()

//Killzones
ny        = time(timeframe.period, sessNY, "America/New_York") and showNy   and showKZ
ldn_open  = time(timeframe.period, sessLO, "Europe/London"   ) and showLdno and showKZ
ldn_close = time(timeframe.period, sessLC, "Europe/London"   ) and showLdnc and showKZ
asian     = time(timeframe.period, sessAS, "Asia/Tokyo"      ) and showAsia and showKZ

//Pivotpoints
ph = ta.pivothigh(3, 1), lPh = fixnan(ph)
pl = ta.pivotlow (3, 1), lPl = fixnan(pl)

//Candles
L_body   = (high - mx < body * perc_Body) and (mn - low < body * perc_Body)
L_bodyUP = body > meanBody and L_body and close > open
L_bodyDN = body > meanBody and L_body and close < open

bsNOTbodyUP = ta.barssince(not L_bodyUP)
bsNOTbodyDN = ta.barssince(not L_bodyDN)
bsIs_bodyUP = ta.barssince(L_bodyUP)
bsIs_bodyDN = ta.barssince(L_bodyDN)

lwst        = math.min(lPh[bsNOTbodyUP[1]], low[bsNOTbodyUP[1]])
hgst        = math.max(high[bsNOTbodyDN[1]], lPl[bsNOTbodyDN[1]])

//Imbalance
imbalanceUP = L_bodyUP[1] and (i_FVG == "FVG" ? low > high[2] : low < high[2])
imbalanceDN = L_bodyDN[1] and (i_FVG == "FVG" ? high < low[2] : high > low[2])

//Volume Imbalance
vImb_Bl = open > close[1] and high[1] > low  and close > close[1] and open > open[1] and high[1] < mn
vImb_Br = open < close[1] and low [1] < high and close < close[1] and open < open[1] and low [1] > mx

if sVimbl
    if vImb_Bl
        Vimbal.unshift(_2ln_lb.new(line.new(n - 1, mx[1], n + 3, mx[1], color = cVimbl), line.new(n, mn, n + 3, mn, color = cVimbl), label.new(n + 3, math.avg(mx[1], mn), text = "VI", color = color(na), textcolor = cVimbl, style = label.style_label_left)))
    if vImb_Br
        Vimbal.unshift(_2ln_lb.new(line.new(n - 1, mn[1], n + 3, mn[1], color = cVimbl), line.new(n, mx, n + 3, mx, color = cVimbl), label.new(n + 3, math.avg(mn[1], mx), text = "VI", color = color(na), textcolor = cVimbl, style = label.style_label_left)))
    if Vimbal.size() > visVim
        pop = Vimbal.pop(), pop.l1.delete(), pop.l2.delete(), pop.lb.delete()

//Fair Value Gap
if barstate.isfirst
    for i = 0 to visBxs - 1
        bFVG_UP.unshift(FVG.new(box(na), false)), bFVG_DN.unshift(FVG.new(box(na), false))
        if i_BPR
            bBPR_UP.unshift(FVG.new(box(na), false)), bBPR_DN.unshift(FVG.new(box(na), false))

if imbalanceUP and per and shwFVG
    if imbalanceUP[1]
        bFVG_UP.get(0).box.set_lefttop(n - 2, low), bFVG_UP.get(0).box.set_rightbottom(n + 8, high[2])
    else
        bFVG_UP.unshift(FVG.new(box.new(n - 2, i_FVG == "FVG" ? low : high[2], n, i_FVG == "FVG" ? high[2] : low, bgcolor = i_BPR ? color(na) : color.new(cFVGbl, 90), border_color = i_BPR ? color(na) : color.new(cFVGbl, 65), text_color = i_BPR ? color(na) : color.new(cFVGbl, 65), text_size = size.small, text = i_FVG), true)), bFVG_UP.pop().box.delete()

if imbalanceDN and per and shwFVG
    if imbalanceDN[1]
        bFVG_DN.get(0).box.set_lefttop(n - 2, low[2]), bFVG_DN.get(0).box.set_rightbottom(n + 8, high)
    else
        bFVG_DN.unshift(FVG.new(box.new(n - 2, i_FVG == "FVG" ? low[2] : high, n, i_FVG == "FVG" ? high : low[2], bgcolor = i_BPR ? color(na) : color.new(cFVGbr, 90), border_color = i_BPR ? color(na) : color.new(cFVGbr, 65), text_color = i_BPR ? color(na) : color.new(cFVGbr, 65), text_size = size.small, text = i_FVG), true)), bFVG_DN.pop().box.delete()

//Balance Price Range
if i_BPR and bFVG_UP.size() > 0 and bFVG_DN.size() > 0
    bxUP = bFVG_UP.get(0), bxDN = bFVG_DN.get(0)
    bxUPbtm = bxUP.box.get_bottom(), bxDNbtm = bxDN.box.get_bottom(), bxUPtop = bxUP.box.get_top(), bxDNtop = bxDN.box.get_top()
    left = math.min(bxUP.box.get_left(), bxDN.box.get_left()), right = math.max(bxUP.box.get_right(), bxDN.box.get_right())
    if bxUPbtm < bxDNtop and bxDNbtm < bxUPbtm
        if left == bBPR_UP.get(0).box.get_left()
            if bBPR_UP.get(0).active
                bBPR_UP.get(0).box.set_right(right)
        else
            bBPR_UP.unshift(FVG.new(box.new(left, bxDNtop, right, bxUPbtm, bgcolor = color.new(cFVGbl, 90), border_color = color.new(cFVGbl, 65), text_color = color.new(cFVGbl, 65), text_size = size.small, text = "BPR"), true, close > bxUPbtm ? 1 : close < bxDNtop ? -1 : 0)), bBPR_UP.pop().box.delete()
    if bxDNbtm < bxUPtop and bxUPbtm < bxDNbtm
        if left == bBPR_DN.get(0).box.get_left()
            if bBPR_DN.get(0).active
                bBPR_DN.get(0).box.set_right(right)
        else
            bBPR_DN.unshift(FVG.new(box.new(left, bxUPtop, right, bxDNbtm, bgcolor = color.new(cFVGbr, 90), border_color = color.new(cFVGbr, 65), text_color = color.new(cFVGbr, 65), text_size = size.small, text = "BPR"), true, close > bxDNbtm ? 1 : close < bxUPtop ? -1 : 0)), bBPR_DN.pop().box.delete()

//FVG breaks
for i = 0 to math.min(bxBack, bFVG_UP.size() - 1)
    upi = bFVG_UP.get(i)
    if upi.active
        upi.box.set_right(bar_index + 8)
        if low < upi.box.get_top() and not i_BPR
            upi.box.set_border_style(line.style_dashed)
        if low < upi.box.get_bottom()
            if not i_BPR
                upi.box.set_bgcolor(color.new(cFVGblBR, 95)), upi.box.set_border_style(line.style_dotted)
            upi.box.set_right(bar_index), upi.active := false

for i = 0 to math.min(bxBack, bFVG_DN.size() - 1)
    dni = bFVG_DN.get(i)
    if dni.active
        dni.box.set_right(bar_index + 8)
        if high > dni.box.get_bottom() and not i_BPR
            dni.box.set_border_style(line.style_dashed)
        if high > dni.box.get_top()
            if not i_BPR
                dni.box.set_bgcolor(color.new(cFVGbrBR, 95)), dni.box.set_border_style(line.style_dotted)
            dni.box.set_right(bar_index), dni.active := false

if i_BPR
    for i = 0 to math.min(bxBack, bBPR_UP.size() - 1)
        upb = bBPR_UP.get(i)
        if upb.active
            upb.box.set_right(bar_index + 8)
            switch upb.pos
                -1 =>
                    if high > upb.box.get_bottom()
                        upb.box.set_border_style(line.style_dashed)
                    if high > upb.box.get_top()
                        upb.box.set_bgcolor(color.new(cFVGblBR, 95)), upb.box.set_border_style(line.style_dotted), upb.box.set_right(bar_index), upb.active := false
                1 =>
                    if low < upb.box.get_top()
                        upb.box.set_border_style(line.style_dashed)
                    if low < upb.box.get_bottom()
                        upb.box.set_bgcolor(color.new(cFVGblBR, 95)), upb.box.set_border_style(line.style_dotted), upb.box.set_right(bar_index), upb.active := false
    for i = 0 to math.min(bxBack, bBPR_DN.size() - 1)
        dnb = bBPR_DN.get(i)
        if dnb.active
            dnb.box.set_right(bar_index + 8)
            switch dnb.pos
                -1 =>
                    if high > dnb.box.get_bottom()
                        dnb.box.set_border_style(line.style_dashed)
                    if high > dnb.box.get_top()
                        dnb.box.set_bgcolor(color.new(cFVGbrBR, 95)), dnb.box.set_border_style(line.style_dotted), dnb.box.set_right(bar_index), dnb.active := false
                1 =>
                    if low < dnb.box.get_top()
                        dnb.box.set_border_style(line.style_dashed)
                    if low < dnb.box.get_bottom()
                        dnb.box.set_bgcolor(color.new(cFVGbrBR, 95)), dnb.box.set_border_style(line.style_dotted), dnb.box.set_right(bar_index), dnb.active := false

//NWOG/NDOG
if barstate.isfirst
    for i = 0 to maxNWOG - 1
        bl_NWOG.unshift(bx_ln.new(box(na), line(na)))
    for i = 0 to maxNDOG - 1
        bl_NDOG.unshift(bx_ln.new(box(na), line(na)))

if dayofweek == dayofweek.friday
    friCp := close, friCi := n

if ta.change(dayofweek)
    if dayofweek == dayofweek.monday and iNWOG
        monOp := open, monOi := n
        bl_NWOG.unshift(bx_ln.new(box.new(friCi, math.max(friCp, monOp), monOi, math.min(friCp, monOp), bgcolor = color(na), border_color = cNWOG2, extend = extend.right), line.new(monOi, math.avg(friCp, monOp), monOi + 1, math.avg(friCp, monOp), color = cNWOG1, style = line.style_dotted, extend = extend.right)))
        bl = bl_NWOG.pop(), bl.b.delete(), bl.l.delete()
    if iNDOG
        cuDOp := open, cuDOi := n
        prDCp := close[1], prDCi := n - 1
        bl_NDOG.unshift(bx_ln.new(box.new(prDCi, math.max(prDCp, cuDOp), cuDOi, math.min(prDCp, cuDOp), bgcolor = color(na), border_color = cNDOG2, extend = extend.right), line.new(cuDOi, math.avg(prDCp, cuDOp), cuDOi + 1, math.avg(prDCp, cuDOp), color = cNDOG1, style = line.style_dotted, extend = extend.right)))
        bl = bl_NDOG.pop(), bl.b.delete(), bl.l.delete()

//Liquidity
for i = 0 to b_liq_B.size() - 1
    x = b_liq_B.get(i)
    if not x.broken
        x.bx.set_right(n + 3), x.ln.set_x2(n + 3)
        if not x.brokenTop
            if close > x.bx.get_top()
                x.brokenTop := true
        if not x.brokenBtm
            if close > x.bx.get_bottom()
                x.brokenBtm := true
        if x.brokenBtm
            x.bx.set_bgcolor(color.new(cLIQ_B, 90)), x.ln.delete()
            if x.brokenTop
                x.broken := true, x.bx.set_right(n)

for i = 0 to b_liq_S.size() - 1
    x = b_liq_S.get(i)
    if not x.broken
        x.bx.set_right(n + 3), x.ln.set_x2(n + 3)
        if not x.brokenTop
            if close < x.bx.get_top()
                x.brokenTop := true
        if not x.brokenBtm
            if close < x.bx.get_bottom()
                x.brokenBtm := true
        if x.brokenTop
            x.bx.set_bgcolor(color.new(cLIQ_S, 90)), x.ln.delete()
            if x.brokenBtm
                x.broken := true, x.bx.set_right(n)

//Order Blocks
[top, btm] = swings(length)

if showOB and per
    if close > top.y and not top.crossed
        top.crossed := true
        minima = max[1], maxima = min[1], loc = time[1]
        for i = 1 to (n - top.x) - 1
            minima := math.min(min[i], minima)
            maxima := minima == min[i] ? max[i] : maxima
            loc := minima == min[i] ? time[i] : loc
        bullish_ob.unshift(ob.new(maxima, minima, loc))
    if bullish_ob.size() > 0
        for i = bullish_ob.size() - 1 to 0
            element = bullish_ob.get(i)
            if not element.breaker
                if math.min(close, open) < element.btm
                    element.breaker := true, element.break_loc := time
            else
                if close > element.top
                    bullish_ob.remove(i)
                else if i < showBull and top.y < element.top and top.y > element.btm
                    blBrkConf := 1
    if blBrkConf > blBrkConf[1] and showLabels
        label.new(top.x, top.y, "▼", color = na, textcolor = bearCss.notransp(), style = label.style_label_down, size = size.tiny)

if showOB and per
    if close < btm.y and not btm.crossed
        btm.crossed := true
        minima = min[1], maxima = max[1], loc = time[1]
        for i = 1 to (n - btm.x) - 1
            maxima := math.max(max[i], maxima)
            minima := maxima == max[i] ? min[i] : minima
            loc := maxima == max[i] ? time[i] : loc
        bearish_ob.unshift(ob.new(maxima, minima, loc))
    if bearish_ob.size() > 0
        for i = bearish_ob.size() - 1 to 0
            element = bearish_ob.get(i)
            if not element.breaker
                if math.max(close, open) > element.top
                    element.breaker := true, element.break_loc := time
            else
                if close < element.btm
                    bearish_ob.remove(i)
                else if i < showBear and btm.y > element.btm and btm.y < element.top
                    brBrkConf := 1
    if brBrkConf > brBrkConf[1] and showLabels
        label.new(btm.x, btm.y, "▲", color = na, textcolor = bullCss.notransp(), style = label.style_label_up, size = size.tiny)

//-----------------------------------------------------------------------------}
//Set Order Blocks
//-----------------------------------------------------------------------------{
if barstate.islast and showOB
    if a_bx_ln_lb.size() > 0
        for i = a_bx_ln_lb.size() - 1 to 0
            item = a_bx_ln_lb.remove(i), item.bx.delete(), item.ln.delete(), item.lb.delete()
    if showBull > 0
        blSz = bullish_ob.size()
        if blSz > 0
            for i = 0 to math.min(showBull, blSz) - 1
                get_ob = bullish_ob.get(i), get_ob.display(bullCss, bullBrkCss, "bl")
    if showBear > 0
        brSz = bearish_ob.size()
        if brSz > 0
            for i = 0 to math.min(showBear, brSz) - 1
                get_ob = bearish_ob.get(i), get_ob.display(bearCss, bearBrkCss, "br")

//-----------------------------------------------------------------------------}
//Fibonacci
//-----------------------------------------------------------------------------{
if barstate.islast
    x1 = 0, y1 = 0.0, x2 = 0, y2 = 0.0
    switch iFib
        "FVG" =>
            if bFVG_UP.size() > 0 and bFVG_DN.size() > 0
                upB = bFVG_UP.get(0).box, dnB = bFVG_DN.get(0).box
                dnFirst = upB.get_left() > dnB.get_left(), dnBottm = upB.get_top() > dnB.get_top()
                x1 := dnFirst ? dnB.get_left() : upB.get_left(), x2 := dnFirst ? upB.get_right() : dnB.get_right()
                y1 := dnFirst ? (dnBottm ? dnB.get_bottom() : dnB.get_top()) : (dnBottm ? upB.get_top() : upB.get_bottom())
                y2 := dnFirst ? (dnBottm ? upB.get_top() : upB.get_bottom()) : (dnBottm ? dnB.get_bottom() : dnB.get_top())
        "BPR" =>
            if bBPR_UP.size() > 0 and bBPR_DN.size() > 0
                upB = bBPR_UP.get(0).box, dnB = bBPR_DN.get(0).box
                dnFirst = upB.get_left() > dnB.get_left(), dnBottm = upB.get_top() > dnB.get_top()
                x1 := dnFirst ? dnB.get_left() : upB.get_left(), x2 := dnFirst ? upB.get_right() : dnB.get_right()
                y1 := dnFirst ? (dnBottm ? dnB.get_bottom() : dnB.get_top()) : (dnBottm ? upB.get_top() : upB.get_bottom())
                y2 := dnFirst ? (dnBottm ? upB.get_top() : upB.get_bottom()) : (dnBottm ? dnB.get_bottom() : dnB.get_top())
        "OB" =>
            oSz = a_bx_ln_lb.size()
            if oSz > 1
                xA = nz(a_bx_ln_lb.get(oSz - 1).ln.get_x1(), a_bx_ln_lb.get(oSz - 1).bx.get_left())
                xB = nz(a_bx_ln_lb.get(oSz - 2).ln.get_x1(), a_bx_ln_lb.get(oSz - 2).bx.get_left())
                AFirst = xB > xA
                yAT = nz(a_bx_ln_lb.get(oSz - 1).ln.get_y1(), a_bx_ln_lb.get(oSz - 1).bx.get_top())
                yAB = nz(a_bx_ln_lb.get(oSz - 1).ln.get_y1(), a_bx_ln_lb.get(oSz - 1).bx.get_bottom())
                yBT = nz(a_bx_ln_lb.get(oSz - 2).ln.get_y1(), a_bx_ln_lb.get(oSz - 2).bx.get_top())
                yBB = nz(a_bx_ln_lb.get(oSz - 2).ln.get_y1(), a_bx_ln_lb.get(oSz - 2).bx.get_bottom())
                ABottom = yAB < yBB
                x1 := AFirst ? xA : xB, x2 := AFirst ? xB : xA
                y1 := AFirst ? (ABottom ? yAB : yAT) : (ABottom ? yBT : yBB)
                y2 := AFirst ? (ABottom ? yBT : yBB) : (ABottom ? yAB : yAT)
        "Liq" =>
            if b_liq_B.size() > 0 and b_liq_S.size() > 0
                xA = nz(b_liq_B.get(0).ln.get_x1(), b_liq_B.get(0).bx.get_left())
                xB = nz(b_liq_S.get(0).ln.get_x1(), b_liq_S.get(0).bx.get_left())
                AFirst = xB > xA
                yAT = nz(b_liq_B.get(0).ln.get_y1(), b_liq_B.get(0).bx.get_top())
                yAB = nz(b_liq_B.get(0).ln.get_y1(), b_liq_B.get(0).bx.get_bottom())
                yBT = nz(b_liq_S.get(0).ln.get_y1(), b_liq_S.get(0).bx.get_top())
                yBB = nz(b_liq_S.get(0).ln.get_y1(), b_liq_S.get(0).bx.get_bottom())
                ABottom = yAB < yBB
                x1 := AFirst ? xA : xB, x2 := AFirst ? xB : xA
                y1 := AFirst ? (ABottom ? yAB : yAT) : (ABottom ? yBT : yBB)
                y2 := AFirst ? (ABottom ? yBT : yBB) : (ABottom ? yAB : yAT)
        "VI" =>
            if Vimbal.size() > 1
                AxA = Vimbal.get(1).l2.get_x1(), AxB = Vimbal.get(1).l1.get_x1()
                BxA = Vimbal.get(0).l2.get_x1(), BxB = Vimbal.get(0).l1.get_x1()
                AyA = Vimbal.get(1).l2.get_y1(), AyB = Vimbal.get(1).l1.get_y1()
                ByA = Vimbal.get(0).l2.get_y1(), ByB = Vimbal.get(0).l1.get_y1()
                ABt = math.min(ByA, ByB) > math.min(AyA, AyB)
                x1 := math.max(AxA, AxB), x2 := math.max(BxA, BxB)
                y1 := ABt ? math.min(AyA, AyB) : math.max(AyA, AyB)
                y2 := ABt ? math.max(ByA, ByB) : math.min(ByA, ByB)
        "NWOG" =>
            if bl_NWOG.size() > 1
                upB = bl_NWOG.get(0).b, dnB = bl_NWOG.get(1).b
                dnFirst = upB.get_left() > dnB.get_left(), dnBottm = upB.get_top() > dnB.get_top()
                x1 := dnFirst ? dnB.get_left() : upB.get_left(), x2 := dnFirst ? upB.get_right() : dnB.get_right()
                y1 := dnFirst ? (dnBottm ? dnB.get_bottom() : dnB.get_top()) : (dnBottm ? upB.get_top() : upB.get_bottom())
                y2 := dnFirst ? (dnBottm ? upB.get_top() : upB.get_bottom()) : (dnBottm ? dnB.get_bottom() : dnB.get_top())
    if iFib != "NONE"
        rt = math.max(x1, x2), _0 = rt == x1 ? y1 : y2, _1 = rt == x1 ? y2 : y1
        df = _1 - _0
        m0236 = df * 0.236, m0382 = df * 0.382, m0500 = df * 0.500, m0618 = df * 0.618, m0786 = df * 0.786, m1618 = df * 1.618
        _diag.setLine(x1, y1, x2, y2), _vert.setLine(rt, _0, rt, _0 + m1618), _zero.setLine(rt, _0, rt + plus, _0)
        _0236.setLine(rt, _0 + m0236, rt + plus, _0 + m0236), _0382.setLine(rt, _0 + m0382, rt + plus, _0 + m0382)
        _0500.setLine(rt, _0 + m0500, rt + plus, _0 + m0500), _0618.setLine(rt, _0 + m0618, rt + plus, _0 + m0618)
        _0786.setLine(rt, _0 + m0786, rt + plus, _0 + m0786), _one_.setLine(rt, _1, rt + plus, _1), _1618.setLine(rt, _0 + m1618, rt + plus, _0 + m1618)

//-----------------------------------------------------------------------------}
//Displacement
//-----------------------------------------------------------------------------{
plotshape(sDispl and per and L_bodyUP ? low  : na, title = "Displacement UP", style = shape.labelup  , color = color.lime, location = location.belowbar)
plotshape(sDispl and per and L_bodyDN ? high : na, title = "Displacement DN", style = shape.labeldown, color = color.red , location = location.abovebar)

//-----------------------------------------------------------------------------}
//background - Killzones
//-----------------------------------------------------------------------------{
bgcolor(per and ny        ? nyCss  : na, editable = false)
bgcolor(per and ldn_open  ? ldnoCss: na, editable = false)
bgcolor(per and ldn_close ? ldncCss: na, editable = false)
bgcolor(per and asian     ? asiaCss: na, editable = false)

//-----------------------------------------------------------------------------}
// MSS alertconditions
//-----------------------------------------------------------------------------{
alertcondition(bullMSSTrig, title = "MSS Bullish", message = "Bullish MSS on {{ticker}} {{interval}}")
alertcondition(bearMSSTrig, title = "MSS Bearish",  message = "Bearish MSS on {{ticker}} {{interval}}")
