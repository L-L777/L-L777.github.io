---
title: 项目搭建能用markdown编辑文章的页面
date: 2025-03-17 16:32:55
tags: ["react","markdown"]
categories: 
    - ["前端"]
    - ["react"]
top_img: https://img0.baidu.com/it/u=2748342842,876019850&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500
cover:  https://img0.baidu.com/it/u=4217862599,249725046&fm=253&fmt=auto&app=138&f=JPEG?w=1500&h=500
---
# **安装依赖**

```tsx
npm install react-markdown jspdf html2canvas
```

# **使用组件**

## 2.1 以在app.tsx使用为例

```tsx

import React from 'react';
import MarkdownNotePage from './MarkdownNotePage';

const App: React.FC = () => {
    return (
        <div>
            <MarkdownNotePage />
        </div>
    );
};

export default App;
```

## 2.2以其他自定义页面为例，如home.tsx页面
```tsx
import React, { useState, useRef } from 'react';
import MDEditor from '@uiw/react-md-editor';
import { useTranslation } from 'react-i18next';
import html2canvas from 'html2canvas';
import jsPDF from 'jspdf';

const Home: React.FC = () => {
  const { t } = useTranslation();
  const [value, setValue] = useState<string>("## 开始编写笔记...");
  const previewRef = useRef<HTMLDivElement>(null);

  const exportToPDF = async () => {
    if (previewRef.current) {
      const canvas = await html2canvas(previewRef.current, {
        scale: 2,
        useCORS: true,
        logging: true,
      });
      
      const imgData = canvas.toDataURL('image/png');
      const pdf = new jsPDF('p', 'mm', 'a4');
      const imgWidth = 190; // A4纸宽度
      const imgHeight = (canvas.height * imgWidth) / canvas.width;
      
      pdf.addImage(imgData, 'PNG', 10, 10, imgWidth, imgHeight);
      pdf.save('笔记.pdf');
    }
  };

  return (
    <div className="p-4 space-y-4">
      <div className="flex justify-between items-center">
        <h1 className="text-2xl font-bold">{t('welcome')}</h1>
        <button 
          onClick={exportToPDF}
          className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        >
          导出PDF
        </button>
      </div>

      {/* 编辑器 */}
      <div className="container mx-auto">
        <MDEditor
          value={value}
          onChange={(val) => setValue(val || '')}
          height={500}
          className="shadow-lg"
        />
      </div>

      {/* 预览 */}
      <div className="container mx-auto p-4 border rounded shadow-lg" ref={previewRef}>
        <MDEditor.Markdown 
          source={value} 
          style={{ 
            whiteSpace: 'pre-wrap',
            backgroundColor: 'white',
            color: 'black'
          }}
        />
      </div>
    </div>
  );
};

export default Home;

```