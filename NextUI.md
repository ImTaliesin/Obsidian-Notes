[[tailwind css]] [[React JS]] [[NextJS]] 

Remember to add nextui to tailwind.config.ts
```
import type { Config } from "tailwindcss";
import {nextui} from '@nextui-org/react';

const config: Config = {
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
    "./node_modules/nextui-org/theme/dist/**/*.{js, ts, jsx, tsx}",
  ],
  theme: {
    extend: {
      backgroundImage: {
        "gradient-radial": "radial-gradient(var(--tw-gradient-stops))",
        "gradient-conic":
          "conic-gradient(from 180deg at 50% 50%, var(--tw-gradient-stops))",
      },
    },
  },
  darkMode: "class", // 'media' or 'class'

  plugins: [nextui()],
};
export default config;
```

[[OAuth]]
```
@auth/core
@auth/prisma-adapter
@next-auth@5.0.0-beta3.
```