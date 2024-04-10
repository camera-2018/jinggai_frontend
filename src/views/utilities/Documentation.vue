<template>
  <div class="card">
    <div class="txt">
      <h2 align="center">井盖隐患智能识别</h2>
      <p>
        随着现代城市化进程的加快和基础设施建设规模的持续扩大，地下管线网络作为城市运行的生命线，包括通信电缆、电力线路、排水管道、供热供气管道等多种关键设施，其建设和延伸促使地面配套的井盖设施也呈现爆炸性增长态势。这些井盖如同城市的脉络节点，遍布大街小巷，连接着地下的“血管”和“神经”，为城市的正常运转提供了不可或缺的基础支撑。，然而井盖设施总量大幅攀升，遍布各地且环境条件复杂多样，易受自然因素损害或位置偏移，从而对道路交通安全、行人人身安全以及重要线缆设施构成潜在威胁。鉴于井盖基数巨大、地域分布广阔且不具备自我监测报告功能，对其安全隐患的排查整改已成为一项紧迫任务。传统的依赖人工定期巡查的管理模式不仅效率低下，而且耗费大量人力物力，应对现代城市管理需求显得力不从心。在此背景下，智能井盖监测系统的研发与应用显得尤为迫切和重要。这类系统通常集成多种先进技术，如物联网传感技术、无线通信技术、地理信息系统（GIS）、大数据分析以及先进的人工智能图像识别技术，例如经过改进和优化的Yolo（You
        Only Look
        Once）模型，能够实时感知井盖的位置变化、物理状态及周边环境信息，一旦发生异常情况，即可迅速通过云平台发送警报至相关部门，大大缩短了隐患发现与处理的时间窗口，有效提高了井盖管理的效率和质量，从而助力智慧城市向着更加安全、便捷、高效的运维目标迈进。
      </p>
      <h3 align="center">模型性能指标</h3>

      <p>（1）mAP评价</p>
      <p>
        经过多次调整试验后，模型最终的性能指标如下表所示：表5中“all
        mAP”表示模型的整体判别性能指标。“good” “broke” “lose” “uncovered” “circle” 分别对应官方的五种分类，右边的数据对应各个分类的判别指标。从表中可看出模型的综合类别判定指标处于较高的水平，但“circle”分类指标相较于其他指标偏低，其原因可能是井圈本身面积较小，模型较难识别；或是在标注过程中难以区分井圈的类别而导致模型对于这一类判别性能较低等。
      </p>
      <table border="1">
        <caption>最终性能指标表</caption>
        <tr>
          <th>all mAP</th>
          <th>0.8102</th>
        </tr>
        <tr>
          <th>good</th>
          <td>0.9364</td>
        </tr>
        <tr>
          <th>broke</th>
          <td>0.9737</td>
        </tr>
        <tr>
          <th>lose</th>
          <td>0.9420</td>
        </tr>
        <tr>
          <th>uncovered</th>
          <td>0.9905</td>
        </tr>
        <tr>
          <th>circle</th>
          <td>0.2083</td>
        </tr>
      </table>
      <br>
      <p>（2）P-R曲线</p>
      <p>
        根据模型输出的各类别分类结果以及对应的置信度，我们绘制出验证集上的P-R曲线，如图13所示：图中可见模型在验证集上对 “good” “broke” “uncovered” “lose” 类性能较好，而在“circle”类上略有不足。基本对应模型的mAP综合评价。
      </p>
      <div align="center">
        <Image src="/pr.jpg" alt="Image" width="100%" preview />
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@media screen and (max-width: 991px) {
  .video-container {
    position: relative;
    width: 100%;
    height: 0;
    padding-bottom: 56.25%;

    iframe {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }
  }
}

.txt {
  margin: 0 20px;
  font-size: 16px;
  margin-bottom: 1em;
}

p {
  text-indent: 2em;
  line-height: 1.8;
  margin-bottom: 1em;
  font-size: 20px;
  color: #333;
}

table {
  border-collapse: collapse;
  width: 100%;
  margin-bottom: 1em;

  th,
  td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: center;
  }

  th {
    background-color: #f2f2f2;
  }

  tr:nth-child(even) {
    background-color: #f2f2f2;
  }

  tr:hover {
    background-color: #ddd;
  }

  caption {
    font-size: 1.2em;
    font-weight: bold;
    margin-bottom: 0.5em;
  }
}
</style>
