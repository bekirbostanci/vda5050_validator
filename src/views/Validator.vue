<script setup lang="ts">
import {Ref, ref} from 'vue';

// UI Components
import {
  ResizableHandle,
  ResizablePanel,
  ResizablePanelGroup,
} from '@/components/ui/resizable';
import Separator from '@/components/ui/separator/Separator.vue';
import {toast} from '@/components/ui/toast';
import {Textarea} from '@/components/ui/textarea';
import {Button} from '@/components/ui/button';
import TopBar from '@/components/TopBar.vue';
import ErrorCard from '@/components/ErrorCard.vue';
import ErrorTable from '@/components/ErrorTable.vue';
import VdaTopicSelect from '@/components/VdaTopicSelect.vue';
import vdaSchemas from '../vda-schemas/index';

// 3rd Party
import VueJsonPretty from 'vue-json-pretty';
import addFormats from 'ajv-formats';
import Ajv2020, {ErrorObject} from 'ajv/dist/2020';
import {Icon} from '@iconify/vue';
import 'vue-json-pretty/lib/styles.css';

enum vdaTopicsEnum {
  AUTO = 'auto',
  VISUALIZATION = 'visualization',
  STATE = 'state',
  ORDER = 'order',
  INSTANT_ACTIONS = 'instantActions',
  CONNECTION = 'connection',
  FACTSHEET = 'factsheet',
}
enum vdaVersionsEnum {
  V1_1_0 = 'v1_1_0',
  V2_0_0 = 'v2_0_0',
  V2_1_0 = 'v2_1_0',
}
type vdaVersions =
  | vdaVersionsEnum.V1_1_0
  | vdaVersionsEnum.V2_0_0
  | vdaVersionsEnum.V2_1_0;

type vdaTopics =
  | vdaTopicsEnum.AUTO
  | 'auto'
  | 'visualization'
  | 'state'
  | 'order'
  | 'instantActions'
  | 'connection'
  | 'factsheet';

const inputData = ref('');
const parsedData = ref(null);
const detectedVersion = ref('');
const detectedTopic = ref('');
const validateErrors: Ref<ErrorObject[]> = ref<ErrorObject[]>([]);
const selectedErrorIndex = ref(0);
const selectedTopic: Ref<vdaTopics> = ref(vdaTopicsEnum.AUTO);

function detectVersion(parsedJson: any): vdaVersions | undefined {
  if (!parsedJson.version) {
    throw new Error('Missing version');
  }

  switch (parsedJson.version) {
    case '1.1.0':
      return vdaVersionsEnum.V1_1_0;
    case '2.0.0':
      return vdaVersionsEnum.V2_0_0;
    case '2.1.0':
      return vdaVersionsEnum.V2_1_0;
    default:
      throw new Error(`Unsupported version: ${parsedJson.version}`);
  }
}

function detectTopic(parsedJson: any): vdaTopics | undefined {
  // TODO: Add more keywords
  if ('lastNodeId' in parsedJson) {
    return vdaTopicsEnum.STATE;
  } else if ('nodes' in parsedJson && 'edges' in parsedJson) {
    return vdaTopicsEnum.ORDER;
  } else if ('agvPosition' in parsedJson) {
    return vdaTopicsEnum.VISUALIZATION;
  } else if ('instantActions' in parsedJson) {
    return vdaTopicsEnum.INSTANT_ACTIONS;
  } else if ('connectionState' in parsedJson) {
    return vdaTopicsEnum.CONNECTION;
  } else if ('factsheet' in parsedJson) {
    return vdaTopicsEnum.FACTSHEET;
  } else {
    throw new Error('Unsupported topic');
  }
}

function beatifyInput() {
  try {
    const parsed = JSON.parse(inputData.value);
    inputData.value = JSON.stringify(parsed, null, 2);
    return parsed;
  } catch (error) {
    toast({
      title: 'Invalid JSON',
      description: 'The JSON data is not valid.',
      variant: 'destructive',
    });
  }
}

function checkValidation() {
  const parsed = beatifyInput();
  parsedData.value = parsed;
  const version = detectVersion(parsed);

  // Topic process
  let topic: vdaTopics | undefined;
  if (selectedTopic.value === vdaTopicsEnum.AUTO) {
    topic = detectTopic(parsed);
  } else {
    topic = selectedTopic.value;
  }

  // Check version and topic
  if (version === undefined || topic === undefined) {
    if (version === undefined) {
      toast({
        title: 'Unsupported version',
        description: 'The JSON data is not valid.',
      });
    }
    if (topic === undefined) {
      toast({
        title: 'Unsupported topic',
        description: 'The JSON data is not valid.',
      });
    }
    throw new Error('Unsupported version or topic');
  }

  // Display topic and version
  detectedTopic.value = topic;
  detectedVersion.value = version;

  // @ts-ignore
  const schema = vdaSchemas[version][topic];
  const ajv = new Ajv2020({
    strict: false,
    verbose: true,
    loadSchema: schema,
    allErrors: true,
  });
  addFormats(ajv);

  const validate = ajv.compile(schema);

  try {
    const isValid = validate(parsed);

    if (validate.errors) {
      validateErrors.value = validate.errors;
    } else {
      validateErrors.value = [];
    }
    if (isValid || ajv.errors) {
      toast({
        title: 'Validation successful',
        description: 'The JSON data is valid.',
      });
    } else {
      toast({
        title: 'Validation failed',
        description: 'The JSON data is not valid.',
        variant: 'destructive',
      });
    }
  } catch (error) {
    toast({
      title: 'Validation failed',
      description: 'The JSON data is not valid.' + error,
    });
  }
}
</script>

<template>
  <div class="h-full w-full">
    <TopBar></TopBar>
    <ResizablePanelGroup
      direction="horizontal"
      class="border w-full"
      style="height: calc(100svh - 36px)"
    >
      <ResizablePanel :default-size="50">
        <Textarea
          class="w-full h-[calc(100svh-90px)] resize-none"
          v-model="inputData"
          placeholder="Type your VDA5050 JSON here..."
        />
        <div class="flex justify-between items-center px-4 py-2">
          <div class="flex items-center">
            <a
              v-if="detectedTopic && detectedVersion"
              class="text-sm text-muted-foreground"
            >
              {{ detectedTopic }} - {{ detectedVersion }}
            </a>
          </div>
          <div class="flex gap-2">
            <VdaTopicSelect @selectedTopic="selectedTopic = $event" />
            <Button @click="inputData = ''" variant="outline">
              <Icon icon="mdi:delete" class="mr-2" />Clear
            </Button>
            <Button @click="beatifyInput()" variant="outline">
              <Icon icon="mdi:format-paint" class="mr-2" />Beautify
            </Button>
            <Button @click="checkValidation()">
              <Icon icon="mdi:send" class="mr-2" />Validate
            </Button>
          </div>
        </div>
      </ResizablePanel>
      <ResizableHandle with-handle />
      <ResizablePanel :default-size="50" v-if="validateErrors.length === 0">
        <div class="h-full overflow-y-scroll">
          <vue-json-pretty :data="parsedData" show-line />
        </div>
      </ResizablePanel>
      <ResizablePanel :default-size="50" v-else>
        <div class="h-full overflow-y-scroll">
          <ErrorCard :validateError="validateErrors[selectedErrorIndex]" />
          <Separator />
          <ErrorTable
            :validateErrors="validateErrors"
            @selectedErrorIndex="selectedErrorIndex = $event"
          />
        </div>
      </ResizablePanel>
    </ResizablePanelGroup>
  </div>
</template>
